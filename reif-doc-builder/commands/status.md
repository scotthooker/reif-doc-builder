---
description: Show the state of a REIF document, or list recent documents
argument-hint: [document-id]
---

Target (may be empty): $ARGUMENTS

If a document id was given, call `get_document` and report its status, quality
score, section approval state, recorded failures and whether PDFs exist.
If no id was given, call `list_documents` and show the most recent ones with
their statuses.

Report what you find plainly. If a document is mid-pipeline (`extracting`,
`assigning`, `enriching`, `generating`), say so rather than treating it as
stuck — a scheduled reaper recovers anything genuinely stalled after 30
minutes.
