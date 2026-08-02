---
name: reif-doc-builder
description: Use when creating, checking, editing or generating REIF property brochures — building a brochure from a builder pack PDF, checking a document's processing status, reviewing or editing section content, or generating the output PDFs. Talks to the REIF platform over its MCP server.
---

# REIF Doc Builder

Drive the REIF brochure platform from Claude. REIF turns a builder's property
pack PDF into three finished sales documents: a portrait property brochure, a
landscape sales presentation, and a single-page highlights sheet.

## Before you start

This skill needs the **REIF MCP server** connected. If the `reif` MCP tools
are not available, stop and tell the user to connect it — do not try to reach
the platform any other way (the HTTP routes assume browser session cookies and
will reject you).

Connection details, including the endpoint and how to get a token, are at
`https://reifweb-production.up.railway.app/llms.txt`. Tokens are minted by an
admin at `/admin/api-tokens` and shown only once. Setup is in
[references/setup.md](references/setup.md).

The server also exposes **resources** and **prompts**, not just tools. Read
`reif://section-catalog` before reasoning about what belongs in a section, and
`reif://builder/<id>` for one builder's full configuration — both beat guessing
from tool output.

## The lifecycle

Work through these in order. Several steps are asynchronous — **poll the
document's status; never assume a step finished.** See
[references/async-steps.md](references/async-steps.md) for exactly which steps
detach and how to poll them; getting this wrong is the most expensive mistake
available here, because a retry pays for the same extraction twice.

1. **Pick a builder.** The builder's config decides which sections exist,
   their order, layout variants and enrichment. Ask the user if it's ambiguous.
2. **Create the document**, then upload the builder pack via a presigned
   upload URL.
3. **Extraction** — AI vision + Document AI pull text, images and plans out of
   the PDF, and harvest the address, price and bed/bath/car into intake data.
4. **Assignment** — extracted content is mapped onto the builder's section
   slots with a confidence score per assignment.
5. **Enrichment** — fills sections that don't come from the PDF: maps, nearby
   places, suburb profile, AI prose.
6. **Review** — read sections, fix what's wrong, then hand to a human.
7. **Generate** — renders all three PDFs together and returns presigned links.

Document statuses: `draft`, `extracting`, `assigning`, `enriching`, `review`,
`review_partial`, `generating`, `complete`, `failed`. `review_partial` means
extraction worked but something was incomplete — it's recoverable and
user-actionable, not a failure.

## Rules that matter

**Never invent property facts.** Prices, dimensions, inclusions, addresses and
land sizes must come from extraction or from the user. These documents go to
real buyers — a fabricated figure is a material error, not a cosmetic one. If a
field is missing, say it's missing and ask.

**Don't approve sections on the user's behalf.** Read and improve them freely,
then surface them for a person to approve. Approval is the human's judgment
that the document is correct.

**Prefer editing over regenerating.** Re-running AI enrichment costs money and
discards human edits. Edit the section text directly unless the user explicitly
asks for a fresh generation. The same goes for re-running extraction.

**Confirm before anything expensive or destructive.** Regenerating PDFs,
re-running extraction, and re-enriching all cost real money or throw away work.
Say what you're about to do and get agreement first.

**Ownership is real.** A token only reaches its owner's documents; admins see
everything. A document you can't access returns not-found rather than a
permission error — treat that as "not yours or doesn't exist", and don't retry
it as though it were a transient failure.

## Working with sections

The authoritative list of section types and their semantics is the
`reif://section-catalog` resource — it is generated from the platform's own
catalogue, so it cannot drift. Do not rely on a remembered list.

A brochure is composed of typed sections (cover, floorplan, inclusions,
proximity maps, suburb profile and so on). The builder's config picks which
appear and in what order.

When reviewing, prioritise by confidence — low-confidence assignments are where
extraction most likely went wrong. Read the section, compare it against the
source content, and fix rather than regenerate.

For prose sections, match the tone already in the document. Australian real
estate context throughout: AUD prices, Australian addresses, drive times
against local CBDs.

## When something goes wrong

- **Stuck in `extracting`/`assigning`** — a scheduled reaper resumes or reverts
  documents stuck over 30 minutes. Report the state; don't hammer retries.
- **`review_partial`** — extraction partly succeeded. Look at the recorded
  failures, tell the user what's missing, and offer to fill the gaps manually
  rather than re-running the whole extraction.
- **`failed`** — read the failure reason before suggesting anything. The
  platform records a specific category (timeout, billing, schema mismatch,
  unconfigured service); the fix differs for each.
- **A tool returns an error** — surface the actual message. Don't paper over it
  with a generic "something went wrong".

## Reporting back

Give the user the document id and a link, state the real status plainly, and
list what still needs their attention. If sections need review, say which and
why. Never claim a brochure is ready unless generation actually completed.
