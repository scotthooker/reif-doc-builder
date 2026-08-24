# Configuring a builder

A **builder** is a housing developer whose brochures the platform generates.
Its configuration decides which sections exist, in what order they print, how
each is laid out and enriched, and which theme it uses. The configuration is
the single source of truth — there is no config file to edit.

## Read first

- `get_builder_config <builderId>` — the whole config, including staging and
  inactive builders that `list_builders` omits.
- `reif://builder-config-schema` — every writable field and which tool writes it.
- `reif://section-catalog` — what each section type means.

## The rule that rejects saves

Every entry in `output_page_order` must be the `section_type` of a section in
`sections[]`. Remove a section and leave its page-order entry behind and the
save is rejected, naming the offender. Change both in the same
`update_builder_config` call.

## Writing

`update_builder_config` writes only the fields you pass; everything else is
left alone. But `sections` is a **full replacement** — pass every section the
builder should keep, not just the ones you are adding, and writing `sections`
also rewrites `output_page_order` to match (a section you drop from `sections`
is dropped from the page order too). Get the current list from
`get_builder_config` and edit it, never write a `sections` array from memory.

`default_inclusions` and `default_usps` are **not writable over MCP** — they
have structured stored shapes and dedicated admin routes handle them. If asked
to change either, say so and point at the admin UI rather than attempting it
through `update_builder_config`.

`manage_builder` handles the lifecycle. New and cloned builders start in
`staging`, and only `active` builders accept new documents — so finish
configuring, then `set_status` to `active`. `delete` is refused while any
document references the builder and needs `confirm` set to the builder id.

## Undo

Every save appends a version. `builder_history list` shows them;
`builder_history restore` re-saves one as the current config, append-only, so
the configuration you replace stays recoverable. That is the undo path — offer
it rather than reconstructing a config by hand.

## Prompts

`set_prompt_override` sets the AI enrichment prompt for a section at global,
builder or document scope, optionally with the model to run it on. Document
beats builder beats global beats the built-in prompt. A new prompt applies the
**next time that section is enriched** — it does not rewrite content that
already exists, so a prompt change alone never touches an already-generated
brochure.

Leaving `instruction` or `modelOverride` out of a `set` call preserves
whatever the section already has at that scope; it does not clear it. There is
currently no way to explicitly clear a `modelOverride` through this tool once
one is set.
