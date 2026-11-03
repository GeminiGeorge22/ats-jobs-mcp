# ATS Jobs MCP: Ashby, Greenhouse, Lever & SmartRecruiters

**New job postings since any date** from the four big applicant tracking systems (Ashby, Greenhouse, Lever, SmartRecruiters), as MCP tools for Claude, Cursor or any MCP client. Give it company board names and a window (24h, 7d, 30d, or a date) and get back only the postings that are new.

Hosted remote MCP server (Streamable HTTP) on [Apify's MCP gateway](https://mcp.apify.com). **Client config plus a small open-source stdio proxy server. No Actor source.**

**Disclosure:** I maintain these Actors on Apify Store (publisher `publicrecords`).

## MCP endpoint

```
https://mcp.apify.com/?tools=publicrecords/ashby-jobs-scraper,publicrecords/greenhouse-jobs-scraper,publicrecords/lever-jobs-scraper,publicrecords/smartrecruiters-jobs-scraper
```

Auth: sign in with Apify (OAuth) when your client prompts, or send an Apify API token as a Bearer token. Runs are billed per result on your own Apify account; see each Store page for current pricing.

## Tools

| Actor | Store page | MCP tool | What it returns |
|---|---|---|---|
| **Ashby Jobs Scraper** | [publicrecords/ashby-jobs-scraper](https://apify.com/publicrecords/ashby-jobs-scraper) | `publicrecords--ashby-jobs-scraper` | Open jobs from any Ashby job board, only those published since a date or window (24h, 7d, 30d): title, location, department, compensation, URL, description. |
| **Greenhouse Jobs Scraper** | [publicrecords/greenhouse-jobs-scraper](https://apify.com/publicrecords/greenhouse-jobs-scraper) | `publicrecords--greenhouse-jobs-scraper` | Open jobs from any Greenhouse job board published since a date or window: title, location, department, URL, description. |
| **Lever Jobs Scraper** | [publicrecords/lever-jobs-scraper](https://apify.com/publicrecords/lever-jobs-scraper) | `publicrecords--lever-jobs-scraper` | Open jobs from any Lever job board created since a date or window: title, location, team, workplace type, URL, description. |
| **SmartRecruiters Jobs Scraper** | [publicrecords/smartrecruiters-jobs-scraper](https://apify.com/publicrecords/smartrecruiters-jobs-scraper) | `publicrecords--smartrecruiters-jobs-scraper` | Open jobs from any SmartRecruiters careers site released since a date or window: title, location, department, URL, description. |

The Apify gateway also exposes helper tools to read run results (`get-actor-run`, `get-dataset-items`).

## Who it's for

- Job seekers and agents: watch target companies for roles posted in the last day or week.
- Recruiters and sourcers: see which companies just opened roles in a function or location.
- Sales and market research: hiring signals (new teams, new locations) from public job boards.

## Example prompts

- List Greenhouse jobs posted in the last 7 days at `<board name>` with 'engineer' in the title.
- Which roles did `<ashby board>` and `<lever board>` open in the last 24 hours? Group by department.
- Pull SmartRecruiters postings released since `<date>` for `<company>` and give me the remote ones.

## Cursor / Claude Code / any remote-MCP client

```json
{
  "mcpServers": {
    "ats-jobs": {
      "url": "https://mcp.apify.com/?tools=publicrecords/ashby-jobs-scraper,publicrecords/greenhouse-jobs-scraper,publicrecords/lever-jobs-scraper,publicrecords/smartrecruiters-jobs-scraper"
    }
  }
}
```

## Claude Desktop (local bridge)

```json
{
  "mcpServers": {
    "ats-jobs": {
      "command": "npx",
      "args": ["-y", "@apify/actors-mcp-server", "--actors", "publicrecords/ashby-jobs-scraper,publicrecords/greenhouse-jobs-scraper,publicrecords/lever-jobs-scraper,publicrecords/smartrecruiters-jobs-scraper"],
      "env": { "APIFY_TOKEN": "YOUR_APIFY_TOKEN" }
    }
  }
}
```

## Local stdio server (open source)

This repo also ships a small MCP server in [`src/`](src/) for clients that only run local stdio servers. It answers `initialize` and `tools/list` locally from [`tools.json`](tools.json), which is generated from each Actor's public input schema and checked for drift in CI and weekly, so any client or directory can inspect the tools with no token. `tools/call` is forwarded to the hosted Apify MCP endpoint above with **your own** `APIFY_TOKEN`; without a token a call returns a clear error instead of running anything. The token is only sent in the `Authorization` header and is never logged.

```json
{
  "mcpServers": {
    "ats-jobs": {
      "command": "npx",
      "args": ["-y", "github:GeminiGeorge22/ats-jobs-mcp"],
      "env": { "APIFY_TOKEN": "YOUR_APIFY_TOKEN" }
    }
  }
}
```

Or from a clone:

```bash
git clone https://github.com/GeminiGeorge22/ats-jobs-mcp.git
cd ats-jobs-mcp && npm install
APIFY_TOKEN=YOUR_APIFY_TOKEN node src/server.js
```

Optional env: `APIFY_ACTORS` (comma-separated subset of the Actors above) and `APIFY_MCP_URL` (full endpoint override).

Tests run against an in-memory mock server, with no network and no token: `npm test`. Requires Node.js 18+.

### Python (uvx/pip)

A matching Python version of this server lives in [`python/`](python/), built on the official `mcp` Python SDK: same tools, same `APIFY_TOKEN`, same hosted endpoint. Requires Python 3.10+.

```json
{
  "mcpServers": {
    "ats-jobs": {
      "command": "uvx",
      "args": ["ats-jobs-mcp-server"],
      "env": { "APIFY_TOKEN": "YOUR_APIFY_TOKEN" }
    }
  }
}
```

Or with pip: `pip install ats-jobs-mcp-server`, then run `ats-jobs-mcp-server`. Python tests: `cd python && pip install -e ".[test]" && env -u APIFY_TOKEN python -m pytest`.

## See also

- [ecommerce-search-rankings-mcp](https://github.com/GeminiGeorge22/ecommerce-search-rankings-mcp): E-commerce search results and product rankings across Amazon, eBay, Walmart, Google Shopping, AliExpress, Shopify and Etsy.
- [ecommerce-sales-mcp](https://github.com/GeminiGeorge22/ecommerce-sales-mcp): E-commerce sales signals: Amazon bestsellers and reviews, eBay sold prices, TikTok Shop and Etsy shop sales.
- [forecast-accuracy-mcp](https://github.com/GeminiGeorge22/forecast-accuracy-mcp): Forecast accuracy and track records: forecasts vs actuals, earnings beats, prediction markets, archived pages and news.
- [sports-betting-stats-mcp](https://github.com/GeminiGeorge22/sports-betting-stats-mcp): Sports betting odds and stats: sportsbook odds, player props, scores, xG, closing lines, horse racing.
- [sports-odds-mcp](https://github.com/GeminiGeorge22/sports-odds-mcp): Live odds from Kambi-powered sportsbooks (BetRivers, Unibet, Paf, Svenska Spel, ATG).
- [etsy-sales-mcp](https://github.com/GeminiGeorge22/etsy-sales-mcp): Etsy search results and Etsy shop sales counters.
- [gov-tenders-mcp](https://github.com/GeminiGeorge22/gov-tenders-mcp): Government tenders, grants, awards and council meetings.

## Registry

Official MCP Registry name: `io.github.GeminiGeorge22/ats-jobs-mcp`

## License

MIT, for this documentation, config and the proxy server code in `src/`. Not affiliated with the sites the Actors read.
