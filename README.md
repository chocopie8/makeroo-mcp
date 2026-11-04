# Makeroo MCP server

Search the [Makeroo](https://makeroo.app/mcp) product directory and list your own product from your
agent. The agent reads your repo or site, previews an honest listing, and submits it only after you
confirm the details and the recipient email.

- Endpoint: `https://makeroo.app/mcp` (Streamable HTTP)
- No authentication, no API key
- Registry name: `app.makeroo/makeroo` ([server.json](./server.json))

> This repository holds the public listing files for the hosted Makeroo MCP server: install
> configs, `server.json`, `llms-install.md` and the logo. The server runs at
> `https://makeroo.app/mcp`; there is nothing to build or run locally.

## Install

[![Add to Cursor](https://cursor.com/deeplink/mcp-install-dark.svg)](https://cursor.com/en/install-mcp?config=eyJ1cmwiOiJodHRwczovL21ha2Vyb28uYXBwL21jcCJ9&name=makeroo)
[![Install in VS Code](https://img.shields.io/badge/VS_Code-Install_Server-0098FF?style=flat-square&logo=visualstudiocode&logoColor=white)](https://insiders.vscode.dev/redirect/mcp/install?config=%7B%22type%22%3A%22http%22%2C%22url%22%3A%22https%3A%2F%2Fmakeroo.app%2Fmcp%22%7D&name=makeroo)
[![Install in VS Code Insiders](https://img.shields.io/badge/VS_Code_Insiders-Install_Server-24bfa5?style=flat-square&logo=visualstudiocode&logoColor=white)](https://insiders.vscode.dev/redirect/mcp/install?config=%7B%22type%22%3A%22http%22%2C%22url%22%3A%22https%3A%2F%2Fmakeroo.app%2Fmcp%22%7D&name=makeroo&quality=insiders)

### Claude Desktop

Settings → Connectors → **Add custom connector**, then paste `https://makeroo.app/mcp`.

Or add it to `claude_desktop_config.json` through [`mcp-remote`](https://www.npmjs.com/package/mcp-remote):

```json
{
  "mcpServers": {
    "makeroo": {
      "command": "npx",
      "args": ["-y", "mcp-remote", "https://makeroo.app/mcp"]
    }
  }
}
```

### Claude Code

```sh
claude mcp add --transport http makeroo https://makeroo.app/mcp
```

### Cursor

`~/.cursor/mcp.json` (or `.cursor/mcp.json` in a project):

```json
{
  "mcpServers": {
    "makeroo": {
      "url": "https://makeroo.app/mcp"
    }
  }
}
```

### VS Code

`.vscode/mcp.json`:

```json
{
  "servers": {
    "makeroo": {
      "type": "http",
      "url": "https://makeroo.app/mcp"
    }
  }
}
```

### Codex

```sh
codex mcp add makeroo --url https://makeroo.app/mcp
```

Or in `~/.codex/config.toml`:

```toml
[mcp_servers.makeroo]
url = "https://makeroo.app/mcp"
```

### Other MCP clients

Point any client that speaks Streamable HTTP at `https://makeroo.app/mcp`. No API key or headers
are needed. Clients configured with an `mcpServers` file take:

```json
{
  "mcpServers": {
    "makeroo": {
      "url": "https://makeroo.app/mcp"
    }
  }
}
```

A client that only starts local (stdio) servers can bridge to it with
`npx -y mcp-remote https://makeroo.app/mcp`.

## Tools

| Tool                    | Input                                                                                                                                                                                                                 | Returns                                                                                                          |
| ----------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------- |
| `search_listings`       | optional `query` (≤100 chars), `category`, `pricing`, `open_source`, `platform`, `limit` (1–50, default 20)                                                                                                           | Live listings, newest first when `query` is omitted, each with `pricing`, `openSource`, `repoUrl` and `platforms` |
| `get_listing`           | `slug`                                                                                                                                                                                                                | One live listing                                                                                                 |
| `list_categories`       | –                                                                                                                                                                                                                     | Valid category ids and names for `submit_listing`                                                                |
| `submit_listing`        | `name` (2–60), `tagline` (5–100), `description` (20–2,000), `url`, `category`, `makerEmail`; optional `logoUrl`, `tags` (up to 5), `pricing`, `openSource`, `repoUrl`, `platforms`                                     | Step 1 of 2. A preview and a `confirmationToken`. Stores and emails nothing                                      |
| `confirm_submission`    | The same fields plus `confirmationToken` (valid 30 minutes)                                                                                                                                                           | Step 2 of 2. Emails a confirm link to `makerEmail` and returns a submission id that stays pending until clicked  |
| `get_submission_status` | `id`                                                                                                                                                                                                                  | `pending`, `review`, `live` or `rejected`, with the verification verdict and reasons                             |

`pricing` is one of `free`, `freemium`, `paid`, `free_trial` (paid after a trial). `platform` and
`platforms` take `web`, `macos`, `windows`, `linux`, `ios`, `android` or `api_cli` (APIs, SDKs and
CLIs). A listing whose value is unknown never matches a filter. Send `pricing`, `openSource` and
`platforms` only when verified.

## Submitting a product

1. The agent gathers facts from your repo or site and calls `submit_listing`.
2. It shows you the preview and the recipient email. Nothing is stored or emailed yet.
3. After you explicitly confirm, it calls `confirm_submission` with the same fields and the token.
   Changed fields or an expired token are rejected.
4. The maker email gets a confirm link that expires after 48 hours. The listing stays pending until
   the maker clicks it. Unconfirmed submissions are rejected.
5. Each confirmed listing is checked by AI before it goes live. The agent polls
   `get_submission_status`.

## Limits

Submissions are rate limited per IP address and per email.

## Privacy

The server has no authentication and no accounts, and keeps no session between calls. The read
tools write nothing to the database; each call is counted by tool name, and the query is not
recorded. `confirm_submission` stores the same fields as the web form, including the maker email,
which is never published. Anyone who holds a submission id can see its status, verdict and reasons.
See the [privacy policy](https://makeroo.app/privacy).

## Try it

> List my product on Makeroo. Read the repo, preview the listing and wait for me to confirm before
> you submit it.

> Search Makeroo for free, open-source developer tools.

> Check the status of my Makeroo submission.

## Support

Questions or problems: [admin@makeroo.app](mailto:admin@makeroo.app) or
[makeroo.app/mcp](https://makeroo.app/mcp).
