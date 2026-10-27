# llms-install.md — connect srezai MCP to Cline

This file is for AI installers: a few exact steps, no guessing.

## What this server is

`srezai-mcp` is an MCP server for **web access**: live web search (strong Russian-web
coverage), clean-Markdown page reading, site map, section crawl, schema-based
extraction, claim verification with quoted sources, ready answers, multi-step
research and YouTube transcripts — 14 tools total.

- Remote endpoint (Streamable HTTP): `https://srezai.ru/api/mcp`
- stdio package (npm): `srezai-mcp`
- Docs: https://srezai.ru/docs/mcp · Connection guide: https://srezai.ru/connect

## Option A — remote server (recommended, nothing to install)

Add this to Cline's MCP settings (`cline_mcp_settings.json`):

```json
{
  "mcpServers": {
    "srezai": {
      "type": "streamableHttp",
      "url": "https://srezai.ru/api/mcp",
      "headers": {
        "Authorization": "Bearer srz_live_YOUR_KEY"
      }
    }
  }
}
```

A key is optional for evaluation: without the header the server works in trial mode
(25 requests/day per IP, max 5 per 10 seconds). For a real key:

1. Open https://srezai.ru/dashboard/keys and create a key (Sign in with email or Telegram).
2. New accounts get 20 starter credits; 1 credit = 0.10 RUB, one search = 1 credit.
3. Alternatively `POST https://srezai.ru/api/v1/auth/quick-register` with
   `{"email": "you@example.com"}` returns a key for agent onboarding.

The server also supports **OAuth 2.1** (Dynamic Client Registration + PKCE S256) for
clients that prefer a browser sign-in instead of a pasted key:
`https://srezai.ru/.well-known/oauth-authorization-server`.

## Option B — stdio fallback

For Cline builds that only speak stdio:

```json
{
  "mcpServers": {
    "srezai": {
      "command": "npx",
      "args": ["-y", "srezai-mcp"],
      "env": { "SREZAI_API_KEY": "srz_live_YOUR_KEY" }
    }
  }
}
```

## Verify the installation

Ask the agent: *"Search the web for the latest Node.js release and give me the link."*
It should call `web_search` and return links. `get_usage` is a free tool for checking
balance and daily limits.

## Claude Code (plugin marketplace)

This repository is also a Claude Code plugin marketplace. The plugin
`srezai-web-access` connects the remote MCP server with no stdio wrapper:

```
/plugin marketplace add srezai-team/srezai-mcp-web-search
/plugin install srezai-web-access@srezai-mcp
```

Authentication: without a key the server runs in free trial mode (25 requests/day
per IP). For paid tools add the key manually:

```
claude mcp add --transport http srezai https://srezai.ru/api/mcp \
  --header "Authorization: Bearer srz_live_YOUR_KEY"
```

Marketplace files: `.claude-plugin/marketplace.json`,
`plugins/srezai-web-access/` (`.claude-plugin/plugin.json` + `.mcp.json`).

## Pricing and limits

- Trial without a key: 25 requests/day per IP.
- Paid: pay-as-you-go, no subscription; top up from 100 RUB.
- 1 credit = 0.10 RUB; search 1 credit, page read 1, extraction 2, verification 8,
  deep research 20 + output tokens.

Issues and support: https://github.com/srezai-team/srezai-mcp-web-search/issues
