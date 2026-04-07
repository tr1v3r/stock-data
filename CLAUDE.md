# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Chinese A-share stock market data repository (~4.1 GB) storing historical daily quotes, technical indicators, and sector-level aggregated data in CSV format. Data is sourced from **EastMoney** and collected by the companion Go tool [tr1v3r/skr](https://github.com/tr1v3r/skr).

This is a **data-only repository** — no application code, no build system, no tests.

## Directory Layout

```
monthly/
  daily_quotes/YYYY-MM.csv          # Per-stock daily trading data (~583 MB total)
  technical_indicators/YYYY-MM.csv   # RSI, KDJ, MA, MACD, Bollinger, OBV, BBI (~1.8 GB total)
  sector_daily_quotes/YYYY-MM.csv    # Aggregated sector/index data (~32 MB, from 2025-09 onward)
```

## Data Conventions

- **Prices are in fen** (1/100 CNY), not yuan — e.g., `1108` means ¥11.08
- Volume is in shares (integer); amount is in CNY (integer)
- Stock symbols are 6-digit codes (e.g., `000001` = Ping An Bank)
- Sector symbols use `BK` prefix (e.g., `BK0420`) — EastMoney sector codes
- Each month gets its own CSV file; data is appended daily on trading days
- Earlier data (2024-01) has many empty fields for bid_ratio, net inflow columns, etc. — these were unavailable during initial historical backfill

## CI/CD

Single workflow: `.github/workflows/daily-quotes-record.yml` (requires `secrets.SKR_REPO_TOKEN`)
- **Schedule**: `30 7 * * 1-5` UTC (15:30 Shanghai time, Mon–Fri, after market close)
- **Process**: Sparse-checkout current month's CSVs only, build `skr` (Go 1.25.0), run `skr eco --export-latest-csv --export-dir ./monthly`, auto-commits if data changed
- **Fail guard**: Workflow fails if `skr` output contains "partial failures"
- **Concurrency**: Single group, no cancellation of in-progress runs
- **Note**: CI only updates `daily_quotes` and `sector_daily_quotes`; `technical_indicators` is generated separately

## Git Conventions

- `chore: update monthly data` — automated data updates
- `ci:` — CI/CD changes
- `fix:` — data fixes
- Single `master` branch; all changes go directly to master
