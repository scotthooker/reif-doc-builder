# REIF Doc Builder

Build REIF property brochures from Claude.

REIF turns a builder's property pack PDF into three finished sales documents —
a property brochure, a sales presentation and a one-page highlights sheet.
This plugin lets you drive that from a conversation with Claude instead of
clicking through the web app.

---

## You need an access token first

**This plugin does nothing on its own.** It talks to the REIF platform, and the
platform only answers callers holding a valid REIF access token.

Ask a REIF administrator for one. They create it at `/admin/api-tokens` in the
REIF web app and send it to you. It looks like:

```
reif_pat_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

Two things worth knowing:

- **It is shown once.** If you lose it, ask for a new one — nobody can look up
  the old value, not even an admin.
- **It is yours alone.** Your token sees your documents. Don't share it or
  paste it into a group chat. If it leaks, ask an admin to revoke it — that
  takes effect immediately.

Anyone can install this plugin. Only people with a token can use it.

---

## Setup

### Claude Code (recommended)

Two commands, then your token.

```
/plugin marketplace add scotthooker/reif-doc-builder
/plugin install reif-doc-builder@reif
```

Then make your token available. On macOS or Linux, add this line to
`~/.zshrc` (or `~/.bashrc`), replacing the placeholder with your real token:

```bash
export REIF_API_TOKEN='reif_pat_your_token_here'
```

Close and reopen your terminal, then start Claude Code and run `/mcp`. You
should see **reif** listed as connected.

### Checking it works

Ask Claude:

> list the REIF builders

If you get a list of builders back, everything is working. If you get an
authorisation error, the token is missing, mistyped, revoked, or your REIF
account has been suspended.

---

## Using it

Once connected, just describe what you want:

> Build a brochure for DevCon at Lot 3 Castle Court, Burnside QLD

> What's the status of document abc-123?

> Review document abc-123 and tell me what needs fixing

There are also three shortcuts:

| Command | What it does |
|---|---|
| `/new-brochure <builder> [address]` | Walks a property through the whole pipeline |
| `/review <document-id>` | Reads every section and ranks what needs attention |
| `/status [document-id]` | Shows a document's state, or lists recent ones |

### Things Claude will not do for you

By design, and deliberately:

- **It will not invent property facts.** Prices, sizes, inclusions and
  addresses come from the source document or from you. If something is
  missing, it will say so and ask rather than fill the gap.
- **It will not approve sections.** It will read them, fix clear errors and
  tell you what needs your judgement — but sign-off stays with a person.
- **It will not silently re-run expensive steps.** Extraction and PDF
  generation cost real money, so it asks first.

### Some steps take a few minutes

Extraction, assignment and PDF generation run for minutes on a full builder
pack. Claude will tell you the work has started and then check back on it.
That is normal — it hasn't stalled, and it should not be restarted just
because it is taking a while.

---

## Getting help

- Something wrong with a brochure, a builder's setup, or your access: talk to
  your REIF administrator.
- Technical detail on what the platform exposes:
  <https://reifweb-production.up.railway.app/llms.txt>

---

## For maintainers

This repository is **published output**. The source of truth is the
`plugins/reif-doc-builder/` directory in the main REIF repository — edit there
and run its publish script. Editing files here directly will be overwritten on
the next publish.
