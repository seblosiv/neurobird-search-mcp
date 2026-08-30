# Installing the Neurobird Search MCP server

Instructions for an AI agent (Cline, Claude Code, Cursor) setting this up on a user's machine.

## What this is

A **remote** MCP server over streamable HTTP. There is nothing to clone, build, install or
run locally. No npm package, no Docker image, no Python environment. Setup is one config
entry plus an API key.

Endpoint: `https://search.neurobird.com/mcp`

## Step 1: get an API key

One unauthenticated request. No signup form, no email, no card:

```bash
curl -X POST https://search.neurobird.com/keys
```

The response contains `api_key`. Every key includes 1,000 free credits a month.

If the user already has a key, use theirs instead and skip this step.

## Step 2: add the server

**Claude Code:**

```bash
claude mcp add --transport http neurobird-search \
  https://search.neurobird.com/mcp \
  --header "Authorization: Bearer <api_key>"
```

**Cline, Cursor, Claude Desktop, VS Code** (JSON config):

```json
{
  "mcpServers": {
    "neurobird-search": {
      "type": "streamableHttp",
      "url": "https://search.neurobird.com/mcp",
      "headers": {
        "Authorization": "Bearer <api_key>"
      }
    }
  }
}
```

Some clients name the transport `http` rather than `streamableHttp`. If the server fails to
appear, try the other spelling before assuming the endpoint is down.

## Step 3: verify

List tools. You should see exactly two: `web_search` and `extract_url`.

A quick end to end check:

```bash
curl -X POST https://search.neurobird.com/mcp \
  -H "Authorization: Bearer <api_key>" \
  -H "Content-Type: application/json" \
  -H "Accept: application/json, text/event-stream" \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/list"}'
```

## Notes for the agent

- **Do not put the key in a file the user commits.** Use the client's own secret handling, or
  an environment variable the config references.
- The key is optional for a first look: without one the server still answers, but quotas are
  tighter. Prefer creating a key.
- `GET /mcp` returns 405 by design. The transport is POST only. That is not an outage.
- Credits and usage: `GET https://search.neurobird.com/balance` with the same bearer token.

## Troubleshooting

| Symptom | Cause | Fix |
|---|---|---|
| Server not listed after adding | transport key spelled `http` vs `streamableHttp` | try the other one |
| 401 | key missing or malformed | header must be `Authorization: Bearer <key>` |
| 405 on a browser visit | GET is not supported, by design | use POST |
| 429 | monthly free credits exhausted | top up, or wait for the monthly reset |
