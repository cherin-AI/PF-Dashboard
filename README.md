# PF/ASIA -- Portfolio Finance Dashboard

Portfolio Finance monitoring dashboard for **APAC equity short
positions**, designed to simulate how a hedge fund treasury or
portfolio finance team tracks borrow costs and financing opportunities.

The dashboard visualizes financing exposure across **Prime Brokers,
markets, sectors and funds**, and highlights potential
**refinancing opportunities to optimize borrow costs**.

**Markets covered**

-   Hong Kong
-   Korea
-   Taiwan
-   Japan

**Technology**

HTML, JavaScript, Chart.js

The dashboard runs as a **standalone HTML application** with simulated
financial data.

The design can be extended to support automated data ingestion via APIs or data pipelines, allowing the dashboard to refresh with updated portfolio datasets.

Data in this project is simulated and does not represent any real portfolio.

------------------------------------------------------------------------

# Dashboard Pages

## Portfolio Snapshot

Provides a high-level view of the portfolio's financing profile.

Key sections

-   **KPI summary** -- exposure, borrow cost, HTB concentration
-   **Exposure by Prime Broker** -- financing distribution across PBs
-   **Sector and Market exposure** -- portfolio concentration analysis
-   **Borrow rate distribution** -- cost structure of the book
-   **Top positions by borrow cost** -- largest financing cost
    contributors

Purpose
Quickly identify **where financing exposure and borrow costs are
concentrated**.

------------------------------------------------------------------------

## Refinancing Opportunities

Compares **current borrow rates vs best available rates across prime
brokers**.

Features

-   Estimated **saving in basis points**
-   Filters by market, fund and HTB classification
-   Sorts by **Daily cost saving**, bps, positon notional $

Purpose
Identify positions where borrow costs can be reduced either through **renegotiation with the current prime broker** or **switching to another broker offering lower rates**, after accounting for crossing costs such as comms and stamp charges.

------------------------------------------------------------------------

## Daily Borrow Availability

Simulated prime broker inventory data.

Displays

-   Borrow rate
-   Hard-to-borrow classification
-   Available shares
-   Security price

Purpose
Monitor **borrow supply and market availability before initiating short
positions**.

------------------------------------------------------------------------

# Project Objective

This dashboard demonstrates how **portfolio finance data can be
structured and visualized** to support:

-   Borrow cost monitoring
-   Prime broker optimization
-   Financing decision support
-   Short inventory analysis

The goal is to replicate the **type of monitoring tools used by hedge
fund portfolio finance and treasury teams**.

------------------------------------------------------------------------

# Preview

/images/dashboard_snapshot.png
/images/refinancing_opportunities.png
/images/daily_availability.png

------------------------------------------------------------------------

# Author

Cherin Kim
Portfolio Finance / Equity Finance

------------------------------------------------------------------------

# Updates & Latest Deployments

Mar 15th, 2025
