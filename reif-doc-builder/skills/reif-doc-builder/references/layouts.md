# Layout, variants and themes

Three different mechanisms control how a brochure looks. Picking the wrong one
is the usual reason a change appears to do nothing.

| | Scope | Written by | Legal values |
|---|---|---|---|
| **Layout config** | One section, one builder | `set_section_layout` | `reif://layout-blocks` |
| **Variant** | A named layout for a section type, per builder or global | `manage_variant` | `reif://variant-schema` |
| **Theme** | PDF colours, fonts and spacing, whole brochure | `manage_theme` | `reif://theme-tokens` |

## Layout config

Block order, block visibility, per-block settings and which variant this
section uses — for one builder only. `reif://layout-blocks` returns, per
section type, an **array of objects** (each carrying an `id` field) — not bare
id strings. **A block id that is not one of those `id` values is silently
ignored when the PDF renders**, so a typo produces no error and no visible
change; read the resource rather than guessing block names. `clear: true`
drops the layout config and falls back to the catalogue default.

## Variants

A variant is a named, reusable layout for one section type. `create` always
makes a **builder-scoped** variant. The only route to a global variant is
`promote` — and `promote` alone changes nothing that renders: it copies the
variant to global with `is_default: false`, so it is not yet the default
anywhere. A later `set_default` on that global copy is what actually reaches
every builder that has not set its own default for that section. `promote`
still requires the confirm token, because it is the step that creates the
fleet-wide object.

Which variant renders, first match wins: **document → builder default → global
default → catalogue**.

## Themes

`manage_theme update` edits the **draft** and changes nothing a reader sees.
`publish` is the only action that moves rendering, and it appends an immutable
version, so it can be restored. `restore_version` also only restores into the
draft — it still needs a `publish` to reach a brochure. Always tell the user
which of these you just did; never say a theme change is live when it is only
drafted.

System themes cannot be edited or deleted — clone one and edit the clone. A
theme in use by any builder or document cannot be deleted; the refusal names
how many.
