# Sports Probabilities MCP

An MCP server that gives AI assistants **Monte Carlo probabilities for the NFL, MLB, NHL, NBA and 17 soccer leagues**, plus a model-versus-market comparison against live Kalshi prices.

Ask in plain language from Claude, ChatGPT or Cursor and get a computed number back — not an estimate the model made up.

**The server runs on Apify:** https://apify.com/commodus67/sports-probabilities-mcp

This repository holds the documentation and the registry manifest (`server.json`). The implementation lives in the Actor above.

## Coverage

| Sport | Teams | Probabilities |
|---|---|---|
| NFL | All 32 | Playoffs, division, wild card, No. 1 seed |
| MLB | All 30 | Postseason, division, wild card, top seed |
| NHL | All 32 | Playoffs, division, wild card, conference top seed, Presidents' Trophy |
| NBA | All 30 | Playoffs, play-in, top six, division, best record, conference finals, conference title, championship |
| Soccer | 17 leagues | Title, top four, continental qualification, playoff, relegation |

Soccer leagues: Premier League, EFL Championship, LaLiga, Serie A, Bundesliga, Ligue 1, Eredivisie, Primeira Liga, Süper Lig, Belgian Pro League, Scottish Premiership, Brasileirão, Liga MX, Liga Profesional (Argentina), Primera A (Colombia), MLS, Austrian Bundesliga. Other ESPN soccer slugs are supported.

## Tools

| Tool | Purpose |
|---|---|
| `get_team_probabilities` | One team's full probability set, current record, projected finish and ranking |
| `get_league_probabilities` | A whole league ranked by probability rather than by points or record |
| `compare_model_vs_market` | Model against live Kalshi prices: edge, expected value after fees, fractional Kelly stake |
| `run_scenario` | A fresh simulation under custom model assumptions, with movement analysis |
| `get_probability_history` | How a team's probability moved across the season, from archived runs |

## Model vs market

`compare_model_vs_market` reads live Kalshi playoff contracts for all four North American leagues — `KXNFLPLAYOFF`, `KXMLBPLAYOFFS`, `KXNHLPLAYOFF` and `KXNBAPLAYOFF` — and returns the gap between model and market, the better side of each contract, expected value after the taker fee and a capped quarter-Kelly stake.

For the NHL and NBA, value calls and stakes stay off until teams have played ten games, as in the underlying Actors; edges are still reported in full. For soccer, or to compare against a bookmaker, pass your own prices in `marketProbabilities`.

## Connect

Streamable HTTP transport, stateless. Endpoint:

```
https://commodus67--sports-probabilities-mcp.apify.actor/mcp
```

Clients that support OAuth (Claude custom connectors, for example) sign in with an Apify account — no token to paste. Other clients send an Apify API token as a bearer token:

```json
{
  "mcpServers": {
    "sports-probabilities": {
      "url": "https://commodus67--sports-probabilities-mcp.apify.actor/mcp",
      "headers": {
        "Authorization": "Bearer YOUR_APIFY_API_TOKEN"
      }
    }
  }
}
```

## How it works

Each answer comes from one of five published Monte Carlo Actors — NFL, MLB, NHL, NBA and soccer — that take live standings and the remaining schedule, play out the rest of the season thousands of times, and count how often each outcome happens. The MCP server calls those Actors and caches the result — it does not reimplement the simulation, so an answer here never disagrees with the same query made directly against the API.

Every response carries the date and the source of the run it came from.

## Pricing

Pay per event, billed through Apify. From **$50.00 per 1,000 probability reads**. Full pricing on the Actor page.

## Note

This is statistics and simulation. It is not betting advice, and it does not produce picks.
