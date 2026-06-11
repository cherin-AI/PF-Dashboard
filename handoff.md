# Handoff — APAC Portfolio Finance Monitor

Single-file dashboard (`index.html`). Static, served as-is, deployed on Vercel.
Local preview: `python3 -m http.server --directory . 8799` then open `http://localhost:8799/index.html`.

---

## Session log — 2026-06-12

Senior-designer / front-end review of the dashboard, then a round of fixes to move it
from "SaaS template" toward "internal desk tool." All changes are in `index.html`.

### Credibility / chrome
- **Removed the fake "live" pulse dot.** Replaced with an honest snapshot timestamp.
- **Header timestamp** now reads `Snapshot as of <date> 08:02 HKT`.
- **Date is HKT-based and trading-day aware.** Computed in `Asia/Hong_Kong`; weekends roll
  back to Friday (data updates Mon–Fri only). Sat→Fri, Sun→Fri, Mon–Fri unchanged.
- **Moved contact + "simulated data" disclaimer out of the header** into a footer
  (`Built by Cherin Kim …` / `Demonstration build · simulated portfolio & borrow data · not investment advice`).

### Data presentation
- **Tabular numerals** (`tabular-nums`) on all table cells, KPIs, and saving figures so
  columns align digit-for-digit.
- **Rate precision fixed to 2 decimals** everywhere (was an inconsistent 3 decimals).
- **De-rainbowed the KPIs** — neutral white; only HTB Positions keeps amber as a risk signal.
- **Brightened the overall text ramp** (`--t1/--t2/--t3`) and hardcoded chart text so every
  data surface reads clearly on the dark theme.

### Page 1 — Portfolio Snapshot
- **Bar charts changed to solid fills.** Sector / Fund / Rate Distribution now solid
  steel-blue (`#5f86bd`) instead of translucent purple. Donuts keep their categorical colors.
- Fixed the muddy maroon "Short Exposure by Fund" bars.

### Top 30 Positions table
- **Added a `Rank` column** (far left, 1–30).
- **Moved `Daily Cost`** to sit right after Rank (removed from the far right). Table is sorted
  by daily cost, so Rank 1 = highest cost.

### Action Items page
- **Removed the colored left-edge accent stripe** on each card (kept the CRITICAL/WARNING/
  OPPORTUNITY badges). Rank numbers are larger and colored by severity.
- **Cards ~40% more compact**; filled the empty right side with a **Counterparty Concentration**
  panel that backs the #1 critical item.
- The concentration panel is a **horizontal bar chart**: Prime A/B/C bars, a dashed red
  **50% soft-limit line**, % labels, and a caption ("Internal soft limit: 50% per counterparty…").
  Bars turn punchy red (`#FF2233`) on breach. Bars at 75% opacity, size-9 labels.

### Daily Avail page
- **Grouped bar charts changed to solid fills at 75% opacity**, keeping the Prime A/B/C
  color coding (purple/teal/orange). Less glare than full opacity, no hollow outline look.

### Misc / theme
- Dimmed the scrollbar thumb (was a bright `#a8bdd6`).
- Softened the active nav tab (purple-tinted pill instead of a hard white box).
- `.gitignore`: added `.gstack/` and `backup/`.

### Tried and reverted
- A full-width **30-day "Daily Borrow Cost" trend chart** and a **Page-1 PB concentration bar**
  (item 6/7 of the review) were built, then reverted at the user's request. Page 1 is back to
  the original six-chart layout.

---

## Outstanding / not done
- Review item **12**: show full dollars at position level (KPIs keep abbreviation).
- A planned **OPPORTUNITY** action-item (synthetic cross-fund duplicate, saving ~$481/day) was
  scoped but not implemented.

## Notes
- `index.html` references `/_vercel/insights/script.js` — 404s locally, resolves on Vercel.
- Charts use Chart.js + chartjs-plugin-datalabels (CDN). Custom charts that bypass the
  `makeChart` factory **must** include `BASE_OPTS` (responsive + `maintainAspectRatio:false`),
  otherwise the canvas stretches horizontally (this bit the concentration chart once).
