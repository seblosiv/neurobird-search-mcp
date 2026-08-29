# Calling the MCP server directly

The server speaks JSON-RPC 2.0 over the streamable HTTP transport, so it can be
driven with nothing but `curl`.

## Initialize

```bash
curl -s -X POST https://search.neurobird.com/mcp \
  -H "Content-Type: application/json" \
  -d '{"jsonrpc":"2.0","id":1,"method":"initialize",
       "params":{"protocolVersion":"2025-06-18","capabilities":{},
                 "clientInfo":{"name":"curl","version":"1"}}}'
```

## List tools

```bash
curl -s -X POST https://search.neurobird.com/mcp \
  -H "Content-Type: application/json" \
  -d '{"jsonrpc":"2.0","id":2,"method":"tools/list"}'
```

## Call web_search

```bash
curl -s -X POST https://search.neurobird.com/mcp \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $NEUROBIRD_KEY" \
  -d '{"jsonrpc":"2.0","id":3,"method":"tools/call",
       "params":{"name":"web_search",
                 "arguments":{"query":"what is reciprocal rank fusion",
                              "search_depth":"advanced","include_answer":true}}}'
```

## Call extract_url

```bash
curl -s -X POST https://search.neurobird.com/mcp \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $NEUROBIRD_KEY" \
  -d '{"jsonrpc":"2.0","id":4,"method":"tools/call",
       "params":{"name":"extract_url",
                 "arguments":{"urls":["https://example.com"],"format":"markdown"}}}'
```
