# Neurobird Search MCP server

A hosted [Model Context Protocol](https://modelcontextprotocol.io) server that gives an
AI agent web search with the page content already extracted, plus clean markdown
extraction of any URL.

There is nothing to install and nothing to run locally. The server is hosted at
`https://search.neurobird.com/mcp` over the streamable HTTP transport.

```bash
claude mcp add --transport http neurobird-search \
  https://search.neurobird.com/mcp \
  --header "Authorization: Bearer $NEUROBIRD_KEY"
```

Get a key with one unauthenticated request. No signup form, no email, no card:

```bash
curl -X POST https://search.neurobird.com/keys
```

Every key carries 1,000 free credits a month.

## Tools

### `web_search`

Search the live web and get ranked results with the relevant passages already
extracted from each page, so a follow up fetch is usually unnecessary.

| Parameter | Type | Default | Notes |
|---|---|---|---|
| `query` | string | required | The search query. |
| `search_depth` | `basic` \| `standard` \| `advanced` | `standard` | `advanced` expands the query and reads more pages. |
| `max_results` | integer | 6 | 1 to 20. |
| `include_answer` | boolean | true | A grounded answer written only from retrieved passages. |
| `include_domains` | string[] | | Only return results from these domains. |
| `exclude_domains` | string[] | | |
| `days` | integer | | Restrict to the last N days. |
| `topic` | `general` \| `news` \| `code` \| `science` \| `finance` | `general` | |

### `extract_url`

Fetch one or more URLs and return their main content as clean markdown, with
navigation, adverts and boilerplate removed. PDFs are converted to text.

| Parameter | Type | Notes |
|---|---|---|
| `urls` | string[] | Required. |
| `query` | string | Trims each page to the passages relevant to it. |
| `format` | `markdown` \| `text` | Default `markdown`. |

## Other MCP clients

The same URL works in any client that supports the streamable HTTP transport.

```json
{
  "mcpServers": {
    "neurobird-search": {
      "type": "http",
      "url": "https://search.neurobird.com/mcp",
      "headers": { "Authorization": "Bearer nbs-..." }
    }
  }
}
```

Tested with Claude Code. Cursor, Windsurf, Zed and the Claude desktop app all accept a
streamable HTTP MCP server in this shape.

## What makes the results different

- **Page passages, not snippets.** Each result carries the part of the page that answers
  the query, extracted from the live page, at no extra credit.
- **Quotes checked against the source.** When an answer is requested, every supporting
  quote is matched back against the extracted source text. A quote that fails is returned
  marked unverified rather than silently dropped, so an agent can audit a claim instead of
  trusting it.
- **Rank fusion across engines.** Results are fused from several engines with reciprocal
  rank fusion, so agreement between independent rankings drives the ordering.
- **Per stage timings** in every response, so latency can be attributed from outside.

## Pricing

Credits: basic or standard search 1, advanced search 2, grounded answer +1,
`extract_url` 1 per page.

| | Per 1,000 searches |
|---|---|
| Free tier | 1,000 credits a month, no card |
| Basic search | $2 |
| Deep search | $4 |
| Deep search with grounded answer | $6 |
| At 1M credits a month | $1 per 1,000 |

## Honest limits

- Median latency on the current single server deployment is about 15 seconds basic,
  22 seconds standard and 28 seconds advanced. Upstream engines and the answer model
  account for nearly all of it. The HTTP streaming endpoint returns ranked links at
  about 5 seconds.
- There is no index of our own. Coverage and freshness inherit from upstream engines.
- Retrieval capacity, not code, is the scaling limit today.

## Links

- Landing page and live demo: https://search.neurobird.com
- API reference: https://search.neurobird.com/api-reference
- OpenAPI: https://search.neurobird.com/openapi.json
- Payment options, machine readable: https://search.neurobird.com/payments
- Status: https://search.neurobird.com/health

## License

MIT. See [LICENSE](LICENSE).
