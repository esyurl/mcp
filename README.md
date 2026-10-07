# esyURL MCP server

A hosted [MCP](https://modelcontextprotocol.io) server that lets AI agents
create and manage short links and QR codes with [esyURL](https://esyurl.fyi):
shorten URLs, render styled QR codes, change where a printed or shared link
points, route visitors by device or country, and read click and scan stats.
The agent can sign itself up; no human account is needed.

- Endpoint: `https://esyurl.fyi/mcp` (Streamable HTTP, stateless)
- Auth: `Authorization: Bearer esy_...`

There is nothing to install or run: this repository describes the hosted
server. The server itself is not open source.

## Get an API key

An agent without a key can create its own account and get one, following
[esyurl.fyi/auth.md](https://esyurl.fyi/auth.md):

```sh
BASE=https://esyurl.fyi
REG=$(curl -s $BASE/agent/identity -H 'content-type: application/json' -d '{"type":"anonymous"}')
KEY=$(curl -s $BASE/oauth2/token \
  --data-urlencode grant_type=urn:ietf:params:oauth:grant-type:jwt-bearer \
  --data-urlencode assertion=$(echo "$REG" | jq -r .identity_assertion) | jq -r .access_token)
echo $KEY
```

Self-signed-up accounts get 100 links, 1000 once a human claims the account by
email.

## Connect

Claude Code:

```sh
claude mcp add --transport http esyurl https://esyurl.fyi/mcp \
  --header "Authorization: Bearer esy_..."
```

Cursor, VS Code and other clients (`.mcp.json` style):

```json
{
  "mcpServers": {
    "esyurl": {
      "type": "http",
      "url": "https://esyurl.fyi/mcp",
      "headers": { "Authorization": "Bearer esy_..." }
    }
  }
}
```

## Tools

| Tool | What it does |
| --- | --- |
| `create_short_link` | Shorten a URL, with an optional custom slug, QR style and redirect rules |
| `list_short_links` | List your links, optionally by tag or group |
| `get_short_link` | One link, with its groups and lifetime visit count |
| `update_short_link` | Change where a link points, its QR style or rules, or pause it; the short URL and printed QR codes keep working |
| `delete_short_link` | Delete a link |
| `test_redirect_rules` | Check which destination a visitor would get (e.g. iPhone to the App Store, Android to Google Play) |
| `get_short_link_visits` | Visits per day, split into QR scans, direct clicks and bots |
| `render_qr_code` | A PNG QR code for a link (scans counted) or any text: module shapes, corner markers, colours, a logo or a photo |
| `create_group`, `list_groups`, `get_group`, `update_group`, `delete_group` | Organise links into groups |
| `add_links_to_group`, `remove_link_from_group` | Group membership |
| `get_group_visits` | Combined visits for a group |
| `get_custom_domain`, `set_custom_domain`, `remove_custom_domain` | Short links on your own domain |

## Pricing

Links, QR codes and stats are free. Self-signed-up accounts pay once per new
custom domain, in USDC with [x402](https://x402.org); `set_custom_domain`
returns the payment requirements first.

## More

- [esyurl.fyi/llms.txt](https://esyurl.fyi/llms.txt): the API for agents
- [esyurl.fyi/openapi.json](https://esyurl.fyi/openapi.json): REST API
- [esyurl/skills](https://github.com/esyurl/skills): the same, as agent skills
  (`npx skills add esyurl/skills`)

## License

MIT, for the contents of this repository.
