# Asynchronous steps

Three tools wrap work that takes longer than a single MCP tool call is allowed
to run: **`run_extraction`**, **`run_assignment`** and **`generate_pdfs`**.

A remote MCP tool call is capped at **60 seconds** by default. Extraction alone
carries a 150-second internal deadline, and generation drives a headless
browser three times. So these tools do not block to completion — they start the
work, wait a short while, and then return one of two things.

## The two outcomes

**It finished inside the call.** You get the real result — element counts,
quality score, rendered PDFs. This is common for dedup-cache hits and small
packs. Nothing special to do.

**It is still running.** You get a response saying so, with
`running: true` and `pollWith: "get_document"`. The work started successfully
and is continuing on the server.

## What to do when it is still running

Poll `get_document` until `status` is one of:

- `review` / `review_partial` — extraction and assignment are done
- `complete` — PDFs are ready
- `failed` — read the recorded failure reason before suggesting anything

Leave a sensible gap between polls. A real builder pack takes minutes, not
seconds; polling every few seconds just burns tokens.

## Do not call the tool again

This is the rule that matters. A second `run_extraction` while the first is
still running does not "retry" anything — it starts a **second** extraction,
pays for a second set of AI vision calls, and can leave the document in a
confusing state. The same applies to `generate_pdfs`.

If you genuinely need to start over, say so and ask the user first.

## If it looks stuck

A document sitting in `extracting`, `assigning`, `enriching` or `generating`
for more than 30 minutes is picked up automatically by a scheduled reaper,
which either resumes it or reverts it to a recoverable state. Report the state
and let the reaper do its job. Do not hammer retries.

`review_partial` is **not** a failure — extraction partly succeeded and the
document is user-actionable. Look at the recorded failures, tell the user what
is missing, and offer to fill the gaps manually rather than re-running the
whole extraction.
