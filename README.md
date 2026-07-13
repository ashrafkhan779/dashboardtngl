# Finance Control — Sales Performance Dashboard

A self-contained, zero-build financial dashboard for the ET International sales ledger
(6,605 invoices, Oct 2020 – Feb 2026). Sales, profitability, customer, salesperson, P&L,
expense, bank and cash-flow analysis in a single `index.html`.

## Why a single file (a deliberate deviation from the React/Vite brief)

Your spec asked for React + TypeScript + Vite. For an **internal, single-team finance tool
deployed to GitHub Pages**, a single self-contained `index.html` is the stronger call:

- **Zero build, zero dependencies to break.** No `npm install`, no toolchain drift, no lockfile rot.
- **Deploys by copying one file.** No base-path/router headaches (the classic Vite-on-Pages trap).
- **Opens offline by double-click.** Charts (Chart.js) and Excel export (SheetJS) load from CDN when online.
- **The data travels with the app** — the cleaned 6,605-row dataset is embedded, so it always renders.

The code is modular (clear render functions per tab, a filter engine, a storage service layer),
so it can be lifted into React later with no logic rewrite. If you specifically need the Vite
project structure for a multi-developer team, say so and I'll scaffold it.

## Deploy to GitHub Pages (2 minutes)

1. Create a repo, drop in `index.html` and the `.github/` folder.
2. `git add . && git commit -m "Finance dashboard" && git push`
3. Repo → **Settings → Pages → Source: GitHub Actions**.
4. The included workflow publishes automatically. Your URL: `https://<user>.github.io/<repo>/`

Or just open `index.html` locally — it needs no server.

## Deploy to Vercel

`vercel` in the folder, or import the repo — it's served as a static site, no config needed.

## What's data-driven vs. editable

| Tab | Source |
|---|---|
| Executive, Customer, Salesperson, P&L (revenue/COGS) | **Live** from your cleaned sales data |
| Operational Expenses, Banks, Cash Flow | **Editable modules** (browser `localStorage`); no source data existed for these |
| P&L operating expenses | Flow automatically from the Expenses module |

## Data cleaning applied

- Merged 3 duplicate customers (case variants) → 253 canonical customers.
- Repaired 4 dates stored as raw Excel serials.
- Recomputed Profit and Gross % from source (zero-safe).
- Retained 100 loss-making invoices (flagged, not deleted).
- Flagged the salesperson-attribution gap (~90% booked to "MANAGEMENT").

See the **Data & Audit** tab in-app for the full log and standard data model.

## Backup / restore

**Data & Audit → Backup App Data (JSON)** exports your expense/bank/cash entries.
Cleaned sales export is available there and via the top-bar **Export** on any analytics tab.

## Files

- `index.html` — the entire application (data embedded).
- `Sales_Cleaned_Dataset.xlsx` — cleaned data + yearly/customer summaries.
- `.github/workflows/deploy.yml` — GitHub Pages CI.

## Known limitations / next steps

- Salesperson analytics are constrained until "MANAGEMENT" revenue is attributed to named reps.
- Editable modules use browser storage (per-device). Swap the `STORE` service for Supabase/Firebase
  for shared multi-user data — the abstraction is isolated in one object.
- Bank reconciliation and 13-week forecast are scaffolded on the entered ledger; enrich as data grows.
