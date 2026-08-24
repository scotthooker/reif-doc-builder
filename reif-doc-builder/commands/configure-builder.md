---
description: Read and change a REIF builder's configuration
argument-hint: <builder-id> [what to change]
---

Configure a REIF builder using the `reif` MCP server. Needs an admin token.

Builder and requested change (may be empty — ask me if so): $ARGUMENTS

1. `get_builder_config` first. Report the builder's status, theme, section list
   and page order plainly.
2. If a change was requested, work out exactly which tool owns it —
   `update_builder_config` for sections, order, theme, profile, defaults and
   enrichment; `set_section_layout` for how one section looks;
   `manage_variant` for named reusable layouts; `manage_theme` for theme
   tokens; `set_prompt_override` for enrichment prompts. Read
   `reif://layout-blocks` or `reif://variant-schema` if the change touches
   layout.
3. **Show me the change as a before/after diff and wait for my agreement.**
   Do not write anything first.
4. Make the change, then say what is now live and what still needs publishing
   or activating.

If anything is refused, tell me the actual reason. Do not retry a refused
destructive action with a confirm token unless I have said to.
