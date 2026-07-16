# Unofficial CSE (cse.lk) API Documentation

> Live-probed documentation of Colombo Stock Exchange public JSON/WebSocket endpoints.
> **Not affiliated with the CSE.** Data may change without notice.

[![Probe](https://github.com/Cookie-Cat21/cse-api-docs/actions/workflows/probe.yml/badge.svg)](https://github.com/Cookie-Cat21/cse-api-docs/actions/workflows/probe.yml)
[![Pages](https://img.shields.io/badge/docs-GitHub%20Pages-blue)](https://cookie-cat21.github.io/cse-api-docs/)

## Why this exists

Community lists (e.g. [GH0STH4CKER](https://github.com/GH0STH4CKER/Colombo-Stock-Exchange-CSE-API-Documentation)) name endpoints but often skip request shapes, failure modes, and WebSocket. This project:

- Verifies each endpoint with an automated **probe harness** (re-run weekly in CI)
- Stores **truncated samples** + last-verified dates
- Documents **STOMP** at `/api/ws`
- Ships **curl + Python** examples
- States **ethics** up front (rate limits, no auth abuse)

Hosted docs: **<https://cookie-cat21.github.io/cse-api-docs/>**

## Quick start

```bash
git clone https://github.com/Cookie-Cat21/cse-api-docs.git
cd cse-api-docs
python3 -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt

# Live probe (polite delays; writes samples/ + catalog/last_probe.json)
python3 scripts/probe.py

# Build static site into site/
python3 scripts/build_site.py

# Local preview
python3 -m http.server 8765 --directory site
# open http://127.0.0.1:8765/
```

## Layout

```
catalog/endpoints.yaml   # source of truth
samples/                 # truncated live JSON
scripts/probe.py         # verifier
scripts/build_site.py    # static HTML docs
docs/                    # markdown sources (ethics, websocket, …)
examples/                # curl + python
site/                    # generated (committed; deployed to GitHub Pages)
```

## Python helper package

```bash
cd python
pip install -e .
python smoke.py
```

Thin wrappers only (`market_status`, `company_info`, `trade_summary`, …). Same ethics as the docs.

## Endpoints (37, live-probed weekly)

Full request/response shapes, curl examples, and samples: **<https://cookie-cat21.github.io/cse-api-docs/>**. Source of truth: [`catalog/endpoints.yaml`](catalog/endpoints.yaml).

**Prices**
- `POST /companyInfoSummery` — per-symbol quote (primary single-name source)
- `POST /tradeSummary` — bulk board, best poller source (all symbols in one call)
- `POST /detailedTrades` — market-wide detailed trades board
- `POST /orderBook` — order-book totals + live bid/ask levels for one symbol
- `POST /todaySharePrice` — short list (~top N), not a full board

**Market**
- `POST /marketStatus` — open/closed status for session gating
- `POST /marketSummery` — session aggregates
- `POST /dailyMarketSummery` — end-of-day market aggregates history
- `POST /allSectors` — sector index rows

**Indices**
- `POST /aspiData` — All Share Price Index snapshot
- `POST /snpData` — S&P Sri Lanka 20 snapshot

**Leaderboards**
- `POST /topGainers` / `POST /topLooses` / `POST /mostActiveTrades` — also mirrored over WebSocket

**Charts**
- `POST /companyChartDataByStock` — per-stock intraday ticks (use `reqSymbolInfo.id` as `stockId`)
- `POST /chartData` — index-scale series only; `symbol=` is ignored
- `POST /daysTrade` — day trade tape for one symbol

**Company**
- `POST /companyProfile` — directors, business summary, logo, contacts
- `POST /financials` — per-symbol annual/quarterly/other report archive + `reqFinancial` statement-line array

**Disclosures**
- `POST /approvedAnnouncement`, `POST /getAnnouncementByCompany` (preferred per-symbol), `POST /announcements` (legacy)
- `POST /getFinancialAnnouncement`, `POST /getNonComplianceAnnouncements`, `POST /getNewListingsRelatedNoticesAnnouncements`, `POST /getBuyInBoardAnnouncements`
- `POST /circularAnnouncement`, `POST /directiveAnnouncement`, `POST /getCOVIDAnnouncements`
- `POST /getGeneralAnnouncementById`, `POST /getAnnouncementById` — detail by id, often 204

**Meta / media**
- `GET /corporateAnnouncementCategory`, `GET /smd/categories`, `GET /notifications`
- `GET /news/web`, `GET /events`, `GET /events/top`

**WebSocket (STOMP over SockJS at `/api/ws`)** — mirrors `aspi`, `snp`, `status`, `summary`, `today-sharePrice`, `top-gainers`, `top-looses`, `most-active-trades`, `daytrade`. See [`docs/WEBSOCKET.md`](docs/WEBSOCKET.md) / the hosted site for topics and request destinations.

## CI

- [`probe.yml`](.github/workflows/probe.yml) — weekly (Mon 06:00 UTC) + on push: live-probes every endpoint, uploads the report.
- [`pages.yml`](.github/workflows/pages.yml) — rebuilds the site and deploys it to GitHub Pages on push to `main`.

## Related

Born from research for [Chime](https://github.com/Cookie-Cat21/Chime), a Telegram-first alerting layer for the CSE that consumes a subset of these endpoints.

## License

Documentation and harness: [MIT](LICENSE). CSE data remains subject to CSE terms; we claim no ownership of exchange data. Nothing here is financial advice.
