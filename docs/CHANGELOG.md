# Changelog

## 2026-07-16 — v0.3.0

- Catalog grew from 35 to 37 endpoints:
  - `POST /financials` — per-symbol annual/quarterly/other report archive
    plus a `reqFinancial` statement-line array (`secId`/`elmId`/`data`).
  - `POST /orderBook` — order-book totals + live bid/ask levels for one
    symbol (already used by the Chime adapter, previously undocumented here).
- Re-probed the full catalog: 37/37 passed.
- README now lists every endpoint grouped by category.

## 2026-07-12 — v0.2.0

- Extracted to a standalone repository:
  [Cookie-Cat21/cse-api-docs](https://github.com/Cookie-Cat21/cse-api-docs)
  (previously staged inside the [Chime](https://github.com/Cookie-Cat21/Chime) monorepo).
- CI moved with it: weekly probe + GitHub Pages deploy now run from this repo.
- Probe User-Agent and Python client now reference this repo.
- `probe.py` / `build_site.py`: force UTF-8 stdout so reports print on Windows
  consoles (cp1252 cannot encode ✅/→).

## 2026-07-12 — v0.1.0

- Initial unofficial CSE API docs kit.
- Catalog of 35+ HTTP endpoints with probe harness.
- STOMP `/api/ws` topic + request destination map from live site JS.
- Samples + static site generator.
- Ethics page.
