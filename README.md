# @pipeworx/jp-customs-ip-export

Which rights holders have an accepted application with Japan Customs to suspend the
EXPORT of goods that infringe their trademark, design or patent (知的財産の輸出差止
申立て)? Search by company name, or by brand or goods keyword.

Part of [Pipeworx](https://pipeworx.io) — an MCP gateway connecting AI agents to 2014+ live data sources. This is an independent, unofficial integration — not affiliated with, endorsed by, or published by the upstream provider.

## Tools

- `jp_customs_ip_export_search(name?, limit?)` — matches the rights-holder company
  (exact or partial after Unicode folding) and the right and goods text. Each match
  gives the right and registration numbers, the goods, the validity period as
  Customs prints it (Japanese era dates), the rights-holder company and its 13-digit
  corporate number where printed, and a link to the entry. Empty `name` lists all.
  Also returns `result`, `total_matches`, `applications_listed`,
  `applications_served`, `entries_unreadable`, `dropped_non_company` and
  `data_as_of`.
- `jp_customs_ip_export_read(link)` — one entry.

## Auth

Keyless.

## Data sources

- Japan Customs (税関, Ministry of Finance), accepted export-suspension applications
  for intellectual property rights. Read from the publisher on request and held in
  the serving process for an hour. `data_as_of` is the day it was read.

## Behaviour worth knowing

- Export list only; the import list is not read. It is a short list of currently
  accepted applications, not a history.
- Only the rights-holder company and corporate number are returned. The contact
  column (law firms, departments, telephone numbers) is never returned. Applicants
  that are not companies are dropped and counted in `dropped_non_company`.
- A listed entry whose page the publisher no longer serves is reported in
  `entries_unreadable` with its right and goods; its rights holder is unknown.
- No hit means no accepted application matching in the list as read now. A list that
  cannot be read, or that changed format, is an error and is never reported as no hit.

## Quick Start

Add to your MCP client (Claude Desktop, Cursor, Windsurf, etc.):

```json
{
  "mcpServers": {
    "jp-customs-ip-export": {
      "url": "https://gateway.pipeworx.io/jp-customs-ip-export/mcp"
    }
  }
}
```

### What this endpoint actually serves

`tools/list` at `https://gateway.pipeworx.io/jp-customs-ip-export/mcp` returns the tools in the table
above **plus the shared Pipeworx meta-tools** — `ask_pipeworx`,
`discover_tools`, `search_within`, `remember`/`recall` and the rest of the
gateway-wide set. So the tool count you see is larger than this table: a
single-pack endpoint currently lists roughly 30 shared tools alongside the
pack's own. The connection's `initialize` response states its exact scope, and
is the authoritative answer for a given day.

This is deliberate, not multiplexing by accident. The meta-tools are what let a
scoped connection answer a question this pack does not cover — via
`ask_pipeworx`, which routes across the whole catalog — without you adding a
second MCP server. There is currently no way to mount a pack endpoint without
them; if the extra schemas cost you more context than the routing is worth,
connect to the full gateway once rather than to several pack endpoints.

Or connect to the full Pipeworx gateway to get every pack's tools listed
directly, instead of just this one's:

```json
{
  "mcpServers": {
    "pipeworx": {
      "url": "https://gateway.pipeworx.io/mcp"
    }
  }
}
```

Both URLs reach the same gateway and the same 2014+ data sources. The
only difference is which pack's tools are listed **directly**; `ask_pipeworx`
reaches all of them from either one.

## No MCP client? Call it over HTTP

```bash
curl -X POST https://gateway.pipeworx.io/v1/tools/jp_customs_ip_export_search \
  -H 'Content-Type: application/json' \
  -d '{"name":"リコー"}'
```

No account needed for the first calls. Inspect any tool: `GET https://gateway.pipeworx.io/v1/tools/jp_customs_ip_export_search`. Find one: `POST https://gateway.pipeworx.io/v1/tools/search_packs` with `{"query":"..."}`.

## Run it from npm

`@pipeworx/mcp-jp-customs-ip-export` is a local stdio bridge to the Pipeworx gateway: your MCP client
starts it with `npx`, and it forwards every call to the same
`https://gateway.pipeworx.io/jp-customs-ip-export/mcp` endpoint shown above. It needs network access;
there is no offline mode.

```json
{
  "mcpServers": {
    "jp-customs-ip-export": {
      "command": "npx",
      "args": ["-y", "@pipeworx/mcp-jp-customs-ip-export"]
    }
  }
}
```

Without a key it calls anonymously, capped at 50 calls a day. A free account
raises that to 200 a day: get a key at https://pipeworx.io/signup and add
`"env": { "PIPEWORX_API_KEY": "<your key>" }` to the server entry.
The key is sent as a Bearer token, the same way a hosted connection sends it,
so calls are metered and billed exactly as they are there.

It lists **only** this pack's tools by default. Set `PIPEWORX_META_TOOLS=1` to
also list `ask_pipeworx` and the other gateway-wide tools.

Behind an HTTPS proxy: Node's built-in `fetch` does not read `HTTPS_PROXY` by
default. Newer Node releases honour it when `NODE_USE_ENV_PROXY=1` is set; check
your Node version's documentation.

## Using with ask_pipeworx

Instead of calling tools directly, you can ask questions in plain English —
this works on the pack endpoint above as well as on the full gateway:

```
ask_pipeworx({ question: "your question about Jp Customs Ip Export data" })
```

The gateway picks the right tool and fills the arguments automatically.

## More

- [Docs and guides](https://pipeworx.io/docs)
- [pipeworx.io](https://pipeworx.io)

## License

MIT
