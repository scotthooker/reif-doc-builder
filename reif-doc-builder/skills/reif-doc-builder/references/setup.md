# Connecting to REIF

The platform is reached over its **remote MCP server**. There is no other
supported route: the ordinary HTTP routes authenticate from browser session
cookies, so an agent calling them directly gets rejected.

Endpoint: `https://reifweb-production.up.railway.app/api/mcp`

## Getting a token

Every caller uses a **personal** API token, not a shared one. Documents are
owner-scoped and telemetry is per-user, so a shared token would break
ownership, audit trails and per-user metrics.

An admin mints tokens at `/admin/api-tokens`. The raw value is shown **once**
and only a hash is stored — if it is lost, revoke it and mint another.
Revoking sets `revoked_at` rather than deleting the row, because the row is an
audit record. Suspending a user kills their tokens immediately, since role and
status are re-read from the live user record on every call.

Treat the token like a password. Do not paste it into a file that gets
committed, and do not echo it back in conversation.

## Claude Code (plugin)

```
/plugin marketplace add scotthooker/reif-doc-builder
/plugin install reif-doc-builder@reif
```

Then set the token in your shell profile so the plugin's MCP entry can read it:

```bash
export REIF_API_TOKEN='reif_pat_…'
```

Restart Claude Code and check the server is connected with `/mcp`.

## Claude Code (without the plugin)

Add to `.mcp.json` in the project root:

```json
{
  "mcpServers": {
    "reif": {
      "type": "http",
      "url": "https://reifweb-production.up.railway.app/api/mcp",
      "headers": { "Authorization": "Bearer ${REIF_API_TOKEN}" }
    }
  }
}
```

The `${REIF_API_TOKEN}` form keeps the secret out of the committed file.

## claude.ai (custom connector)

Add a custom connector pointing at the same `/api/mcp` URL and supply the
token as an `Authorization: Bearer …` header.

The server speaks Streamable HTTP with plain JSON responses (no SSE), protocol
version `2025-11-25`, and advertises `tools`, `resources` and `prompts`.

## Checking it works

Ask for the builder list. If `list_builders` returns builders, the connection,
the token and the user's permissions are all good. A `401` means the token is
missing, malformed, revoked, or belongs to a suspended user.
