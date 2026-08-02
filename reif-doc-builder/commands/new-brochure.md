---
description: Build a REIF brochure from a builder pack, end to end
argument-hint: <builder-id> [address]
---

Build a REIF brochure using the `reif` MCP server.

Builder and address (may be empty — ask me if so): $ARGUMENTS

Work the lifecycle in order and report the result of each step before moving on:

1. Confirm the builder id is real and active (`list_builders`).
2. `create_document`, then `create_upload_url` and upload the builder pack.
3. `run_extraction` — report element count and any gaps.
4. `run_assignment` — report the quality score and low-confidence slots.
5. Read the sections back and tell me which need my attention, worst first.

Then stop. Do not approve sections and do not generate PDFs until I say so.

Extraction, assignment and generation are asynchronous: if a tool reports the
work is still running, poll `get_document` rather than calling it again.

Never invent property facts — prices, dimensions, inclusions, addresses and
land sizes come from extraction or from me.
