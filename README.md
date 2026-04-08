# stock-data

Chinese A-share stock market historical data repository, storing daily quotes and sector-level aggregated data in CSV format.

Data is sourced from **EastMoney** and collected by [tr1v3r/skr](https://github.com/tr1v3r/skr).

## Data Coverage

| Dataset | Period | Files |
|---------|--------|-------|
| Daily Quotes | 2025-01 ~ present | 16 files, ~386 MB |
| Sector Daily Quotes | 2025-09 ~ present | 8 files, ~32 MB |

Updated automatically on every trading day after market close.

## Directory Layout

```
monthly/
  daily_quotes/YYYY-MM.csv          # Per-stock daily trading data
  sector_daily_quotes/YYYY-MM.csv   # Aggregated sector/index data
```

## CSV Columns

Header example from `daily_quotes`:

```
symbol, trade_date, pre_close, open, close, high, low, avg,
limit_up, limit_down, change, change_percent, volume, amount,
turnover_rate, volume_ratio, bid_ratio, bid_diff,
buy_volume, sell_volume,
main_net_inflow, super_inflow, super_outflow, super_net_inflow,
big_inflow, big_outflow, big_net_inflow,
mid_inflow, mid_outflow, mid_net_inflow,
small_inflow, small_outflow, small_net_inflow,
main_net_flow_ratio, super_net_flow_ratio, big_net_flow_ratio,
mid_net_flow_ratio, small_net_flow_ratio,
total_market_cap, circ_market_cap, pe, pb, roe,
data_source, created_at
```

## Data Conventions

- **Prices are in fen** (1/100 CNY), not yuan — e.g., `1108` means ¥11.08
- Volume is in shares (integer); amount is in CNY (integer)
- Stock symbols are 6-digit codes (e.g., `000001` = Ping An Bank)
- Sector symbols use `BK` prefix (e.g., `BK0420`) — EastMoney sector codes
- Each month gets its own CSV file; data is appended daily on trading days
- Earlier data (2025-01) has many empty fields for limit_up, limit_down, bid_ratio, net inflow columns, etc. — these were unavailable during initial historical backfill

## Automation

A GitHub Actions workflow runs at 15:30 Shanghai time (Mon–Fri, after market close) to fetch the latest trading data and auto-commit. It also supports manual triggering via `workflow_dispatch`.
