# Claude Code

```bash
export NEUROBIRD_KEY="$(curl -s -X POST https://search.neurobird.com/keys | python3 -c 'import json,sys;print(json.load(sys.stdin)["api_key"])')"

claude mcp add --transport http neurobird-search \
  https://search.neurobird.com/mcp \
  --header "Authorization: Bearer $NEUROBIRD_KEY"
```

Then ask Claude Code something that needs the live web. It will call `web_search`
and receive ranked sources with the relevant page passages already extracted.

To confirm the server is reachable and see the tool list:

```bash
curl -s -X POST https://search.neurobird.com/mcp \
  -H "Content-Type: application/json" \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/list"}' | python3 -m json.tool
```
