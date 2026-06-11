# Product Requirements Document
## APAC Portfolio Finance Monitor

**Version:** 1.0
**Date:** 2026-03-19
**Author:** Cherin Kim

---

## 1. Problem

Portfolio finance teams at APAC-focused hedge funds manage short positions across multiple prime brokers (PBs) and funds simultaneously. The current workflow lacks a unified view of borrow costs, availability, and cross-PB rate differentials, making it difficult to:

- Identify where the fund is overpaying on borrow rates compared to cheaper alternatives at other PBs
- Spot the same ticker being borrowed across multiple funds at different rates (cross-fund inefficiency)
- Surface concentration risk when too much short exposure sits with a single prime broker
- Quickly act on daily stock availability from each PB

Without a consolidated dashboard, these decisions require manual reconciliation across PB portals, spreadsheets, and emails — creating lag, missed savings, and operational risk.

---

## 2. Objective

Build a single-page, browser-based portfolio finance dashboard that:

1. Gives the portfolio finance team a real-time snapshot of short exposure, borrow costs, and rate composition
2. Surfaces refinancing opportunities by comparing each position's current borrow rate against the best available rate across all PBs
3. Displays daily securities availability per PB with filtering and sorting
4. Auto-generates prioritized action items (cost savings, concentration risk, cross-fund rate arbitrage)
5. Enables one-click export of rerate targets to send directly to prime brokers

**Primary success metric:** Reduction in daily borrow cost through actioned refinancings and rerates.

---

## 3. Users

| User | Role | Primary Need |
|---|---|---|
| Portfolio Finance Analyst | Day-to-day operator | Monitor costs, identify refi opps, export rerate lists |
| Head of Portfolio Finance | Oversight | Daily KPI snapshot, concentration risk, action items |
| Trader / PM | Occasional consumer | Confirm borrow availability before initiating new shorts |

**User context:** APAC markets (HK, KR, TW, JP); multi-fund structure; 3 prime broker relationships.

---

## 4. Use Cases

### UC-1: Morning Cost Review
A portfolio finance analyst opens the dashboard each morning to check overnight changes in daily borrow cost, HTB position count, and total short exposure across funds and PBs.

### UC-2: Identify Refinancing Opportunities
The analyst navigates to the Refinancing Opps page, filters by current PB, and sorts by Daily Saving ($) to find positions where a cheaper rate exists at another PB or via a rerate at the same PB. They export a CSV to send to the target PB.

### UC-3: Check Daily Availability
Before entering a new short, a trader checks the Daily Avail page for a specific PB to confirm shares are available and at what rate type (GC/Warm/HTB).

### UC-4: Act on Priority Items
The analyst opens Action Items to get today's ranked list of the most impactful moves — critical concentration alerts, top 5 cost-saving rerates, and cross-fund borrow inefficiencies — without needing to manually search.

### UC-5: Cross-Fund Borrow Consolidation
The analyst uses Action Items to identify tickers shorted across multiple funds at different rates, then consolidates borrowing at the cheapest PB/rate to reduce total cost.

---

## 5. Core Features

### Page 1 — Portfolio Snapshot
- **KPI Cards:** Total Short Exposure (USD notional), Daily Borrow Cost (+ annualized estimate), HTB Position Count (% of legs, daily cost), Active Short Positions count
- **Charts:**
  - Short Exposure by PB (donut)
  - Short Exposure by Sector, top 10 (horizontal bar)
  - Short Exposure by Fund (bar)
  - Short Exposure by Market (donut)
  - Borrow Cost by Rate Type — HTB / Warm / GC (donut)
  - Borrow Rate Distribution by bucket (vertical bar)
- **Top 30 Table:** Top 30 positions ranked by daily borrow cost; columns: Ticker, Name, Market, Sector, PB, Fund, Rate Type, Side, Shares, Notional, Rate%, Daily Cost

### Page 2 — Refinancing Opportunities
- **KPI Cards:** Total Daily Saving Opp, Annualized, HTB Daily Saving, Warm Daily Saving, Max Single Saving (bps)
- **Filter Bar 1:** Saving threshold chips (All / ≥25 bps / ≥50 bps / ≥100 bps); Rate Type (All / HTB / Warm / GC)
- **Filter Bar 2:** Current PB dropdown, Market dropdown, Fund dropdown, Sort (Daily Saving $ / Saving bps / Notional)
- **Refi Table:** Ticker, Name, Market, Sector, Fund, Rate Type, Current PB + Rate, Best PB + Rate, Saving (bps), Notional, Daily Saving ($); top 5 by saving highlighted
- **Export CSV:** Exports filtered list as `{PB}_rerate_{DDMMYYYY}.csv` with columns: Ticker, Current Rate%, Target Rate%

### Page 3 — Daily Availability
- **PB Tabs:** Prime A / Prime B / Prime C / All PBs
- **Per-PB KPIs:** Total names & notional, HTB/Warm name count + median rates, Overall median rate, Rate range, New names count (available but not in current position)
- **Rate Distribution Chart:** Bucketed histogram per PB
- **New Names Table:** Names available at the PB but not currently in the portfolio; columns: Ticker, Name, Market, Sector, Rate Type, Rate%, Available Shares
- **Full Availability Table:** All available names with column filtering (ticker search, name search, multi-select dropdowns for Market/Sector/Rate Type/In-Pos), multi-column sort (click header; shift-click for secondary sort), In-Pos indicator (✓ / –)
- **All PBs View:** Combined table across all 3 PBs with Prime column; cross-PB rate comparison chart for top borrow rates

### Page 4 — Action Items
- Auto-generated, ranked list of up to 5 priority items, refreshed on page visit
- **Severity levels:** CRITICAL (red), WARNING (amber), OPPORTUNITY (green)
- Each item shows: rank, severity badge, action text, supporting detail, daily saving amount (where applicable)
- **Three rule types** (see Section 7)

---

## 6. Data Schema

### Position Record (`POS`)
| Field | Type | Description |
|---|---|---|
| `ticker` | string | Exchange ticker (e.g., `6701 JP`) |
| `name` | string | Company name |
| `country` | enum: HK, KR, TW, JP | Market / exchange country |
| `sector` | string | GICS sector |
| `fund` | string | Fund name |
| `side` | enum: Short, Long | Position direction |
| `abs_shares` | integer | Share quantity (absolute) |
| `price_usd` | float | Price in USD |
| `gross_exposure_usd` | float | `abs_shares × price_usd` |
| `borrow_rate_pct` | float | Annual borrow rate (%) |
| `rate_type` | enum: GC, Warm, HTB | Rate classification |
| `daily_borrow_cost_usd` | float | Computed daily cost in USD |
| `prime_broker` | enum: Prime A, Prime B, Prime C | Assigned prime broker |

### Availability Record (`AVAIL[PB]`)
| Field | Type | Description |
|---|---|---|
| `ticker` | string | Exchange ticker |
| `name` | string | Company name |
| `country` | enum: HK, KR, TW, JP | Market |
| `sector` | string | GICS sector |
| `borrow_rate_pct` | float | Available borrow rate at this PB (%) |
| `rate_type` | enum: GC, Warm, HTB | Rate classification |
| `available_shares` | integer | Shares available to borrow |
| `price_usd` | float | Price in USD |

### Reference Data
- **Prime Brokers:** Prime A, Prime B, Prime C
- **Funds:** Asia Long/Short, Asia Alpha Master, Quant Asia
- **Markets:** HK (Hong Kong), KR (Korea), TW (Taiwan), JP (Japan)

---

## 7. Logic / Calculations

### Rate Type Classification
| Label | Threshold | Color |
|---|---|---|
| GC (General Collateral) | Rate < 0.5% | Gray |
| Warm | 0.5% ≤ Rate ≤ 5% | Orange |
| HTB (Hard-to-Borrow) | Rate > 5% | Amber |

### Daily Borrow Cost
```
daily_borrow_cost_usd = (borrow_rate_pct / 100) × gross_exposure_usd / 360
```
360-day convention (standard for securities lending).

### Annualized Borrow Cost
```
annualized_cost = daily_borrow_cost_usd × 360
```

### Refinancing Saving
For each position, find the lowest borrow rate available across all 3 PBs for the same ticker:
```
best_rate = min(AM[A][ticker], AM[B][ticker], AM[C][ticker], current_rate)
saving_bps = round((current_rate - best_rate) × 100)
saving_usd_daily = (current_rate - best_rate) / 100 × gross_exposure_usd / 360
```
A position shows as a refinancing opportunity only if `saving_bps > 0`.

### Action Item Rules

**Rule 1 — Concentration Risk**
Flag CRITICAL if any single PB holds > 50% of total short exposure:
```
concentration_pct = pb_gross_exposure / total_gross_exposure
trigger if concentration_pct > 0.50
```

**Rule 2 — Top Cost Savings**
Take the top 5 positions by `saving_usd_daily` where `saving_bps > 0`. For each:
- If `best_pb ≠ current_pb` → recommend moving position to best PB
- If `best_pb = current_pb` → recommend rerate negotiation with current PB

**Rule 3 — Cross-Fund Rate Duplication**
Identify tickers shorted across multiple funds at different borrow rates:
```
spread = max_rate_across_legs - min_rate_across_legs
daily_saving = sum over higher-rate legs of: (leg_rate - best_rate) / 100 × leg_exposure / 360
severity = 'warning' if spread > 2%, else 'opp'
```

### Action Item Priority Sort
1. CRITICAL items first
2. Within severity group: sorted by `sort_key` descending (`saving_usd_daily` for savings rules, `1e9` for concentration to always rank first within CRITICAL)

### Availability Notional
```
available_notional_usd = available_shares × price_usd
```

---

## 8. Constraints

### Technical
- **Single HTML file** — no server, no build step, no database; all logic and data embedded in one `.html` file
- **Static data** — no live API connections; data reflects a snapshot in time (simulated for demo purposes)
- **Client-side only** — runs entirely in the browser; no authentication or session management
- **Chart.js dependency** — visualization uses Chart.js 4.4.0 + chartjs-plugin-datalabels 2.2.0 (CDN)
- **Display limits** — Refi table shows max 150 rows; Availability table shows max 250 rows per render; Top 30 positions table capped at 30

### Business
- **360-day year convention** — daily cost and annualized projections use 360 (not 365) per securities lending market convention
- **Short positions only** — all positions in scope are short; long exposure is not tracked for borrow cost purposes
- **USD denomination** — all monetary values are in USD; prices are pre-converted; no FX logic in-scope
- **No partial fills or settlement logic** — availability is treated as a binary quantity; no haircuts or minimum lot sizes applied
- **Rate type thresholds are fixed** — GC/Warm/HTB cutoffs (0.5%, 5%) are hardcoded constants

---

## 9. Assumptions

1. **One position per ticker per fund per PB** — the data model assumes a single borrow leg per fund/PB/ticker combination; blended rates are not represented
2. **Availability rates are actionable** — borrow rates shown in the availability dataset are assumed to be current, executable quotes (not indicative)
3. **Best rate is always cross-PB** — the refinancing engine considers all 3 PBs for every position; it does not restrict refi options to the current PB
4. **360-day accrual** — consistent with standard securities lending industry convention; does not adjust for weekends or holidays
5. **Rate type thresholds are stable** — GC < 0.5%, Warm 0.5–5%, HTB > 5% are static classification rules and do not vary by market
6. **Prices are USD-equivalent** — all `price_usd` values have already been converted from local currency; no real-time FX rate is required
7. **Action items are computed fresh on page view** — no caching or persistence of action item state between sessions
8. **No position-level P&L** — the dashboard tracks borrow cost only; mark-to-market, unrealized P&L, and financing charges beyond borrow rate are out of scope
9. **Fund names are fixed** — the three fund structures (Asia Long/Short, Asia Alpha Master, Quant Asia) are static enumeration; no dynamic fund registration

---

## 10. Future Extensions

### Near-Term
- **Live data feed integration** — connect to PB APIs or internal OMS/PMS to replace static data with intraday position and rate updates
- **Historical trend view** — track daily borrow cost and HTB count over time; surface positions with accelerating rate increases
- **Alerts / notifications** — push or email alerts when a position's rate spikes beyond a threshold or a large saving opportunity appears
- **User authentication** — restrict access to authorized users; support role-based views (analyst vs. read-only PM)

### Medium-Term
- **Multi-currency support** — display notionals in local currency alongside USD; include live FX rates for HKD, KRW, TWD, JPY
- **Rerate workflow** — track the status of rerate requests (sent → pending → confirmed); reconcile confirmed rates against positions
- **PB scorecard** — rank prime brokers by fill rate on availability, rate competitiveness, and response time
- **Automated CSV delivery** — schedule daily rerate export to be emailed directly to designated PB contacts

### Long-Term
- **Scenario modeling** — simulate the cost impact of moving a given % of HTB positions to GC; model cost under different rate environments
- **Market-level borrow analytics** — show borrow market tightness by sector/country over time (e.g., short interest, days to cover)
- **Integration with order management** — one-click transfer of refinancing recommendations into a trade or borrow instruction workflow
- **API layer** — expose portfolio data and refi recommendations via REST API for downstream consumption by risk systems or reporting tools
