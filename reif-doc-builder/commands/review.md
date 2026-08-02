---
description: Review a REIF document and rank what needs attention
argument-hint: <document-id>
---

Review REIF document: $ARGUMENTS

1. `get_document` first — report status, quality score and recorded failures.
2. Read every section. Judge whether the content belongs there and whether it
   reads like finished sales copy.
3. Rank what needs attention, worst first, with reasons. Low-confidence
   assignments are where extraction most likely went wrong.
4. Fix unambiguous errors (wrong section, truncated text, OCR damage) with
   `update_section_content`. Show me anything needing judgement about the
   property itself instead of guessing.

Do not approve anything — approval is my decision. Finish with a list of what
still needs me.
