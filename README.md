# Finance Control — Sales Performance Dashboard

A self-contained financial dashboard for the sales ledger (6,605 invoices, Oct 2020 – Feb 2026):
sales, profitability, customer multi-year comparison, salesperson, P&L, expenses, banks and cash flow.
Orange/white futuristic UI, logistics iconography, data labels on charts, uploadable logo.

## Files

| File | What it is |
|---|---|
| `index.html` | The whole app (HTML + CSS + JS). Loads `data.json` at runtime. |
| `data.json`  | Cleaned, encoded dataset the dashboard reads. |
| `convert.py` | Regenerates `data.json` from the raw Excel file. |
| `README.md`  | This file. |

## Important: it must be *served*, not opened by double-click

`index.html` loads `data.json` with `fetch()`. Browsers block `fetch()` on `file://` for
security, so double-clicking the file shows a "could not load data.json" screen (not blank —
it tells you what to do). Two correct ways to run it:

**A) GitHub Pages (what you want)** — works automatically, no extra steps.

1. Create a new repo and upload `index.html`, `data.json`, `convert.py`, `README.md`.
2. Repo → **Settings → Pages → Build and deployment → Source: Deploy from a branch → `main` / root** → Save.
3. Wait ~1 minute. Your dashboard is live at `https://<your-username>.github.io/<repo-name>/`.

**B) Preview locally before pushing** — run a one-line web server in the folder:

```bash
python -m http.server
```

Then open `http://localhost:8000`.

## Updating the data

When you get a new Excel export, drop it in the folder and run:

```bash
pip install pandas numpy openpyxl        # first time only
python convert.py "Your New File.xlsx"   # writes a fresh data.json
```

Commit the new `data.json` and GitHub Pages updates itself. The script expects a sheet named
`Input Data` with these columns: `Invoice#, Invoice Date, Customer Name, Quarter, Month, Years,
Mode, Invoice Amount, Cost, Sales Person`.

## What the dashboard does

- **Dashboard** — KPI cards with year-on-year deltas, yearly performance, sales by transport mode
  (plane/ship/truck/etc.), monthly trend, profit bridge, top-20 customers, salesperson summary.
- **Customer Analysis** — pick a customer and up to **three years** for a side-by-side comparison
  table (Sales, COGS, Gross Profit, Margin %, Invoices, Avg Invoice) with Δ vs the prior year,
  a grouped chart, and full invoice history.
- **Salesperson Performance** — leaderboard, margin-quality bubble chart, monthly trend.
- **Profit & Loss** — revenue/COGS pulled live from sales; operating expenses pulled from the
  Expenses module; year-on-year variances.
- **Operational Expenses / Bank Management / Cash Flow** — editable modules stored in your browser
  (`localStorage`). No source data existed for these, so you enter records; they feed the P&L and
  cash-flow runway automatically.
- **Data & Audit** — the cleaning log, standard data model, and exports.

**Filters** (Year, Mode, Salesperson multi-select with an *All* option; Quarter, Month, Customer)
drive every analytics view. Upload your **logo** via the mark or "Upload logo" under the brand.

## Data cleaning applied by convert.py

- Merged 3 duplicate customers that differed only by case → 253 canonical customers.
- Repaired 4 dates stored as raw Excel serial numbers.
- Recomputed Profit and Gross % from source (zero-safe).
- Retained 100 loss-making invoices (real, kept for margin analysis).
- Salesperson attribution note: ~90% of revenue is booked to a generic "MANAGEMENT" pool in the
  source, which limits rep-level analysis until attribution improves.

## Notes / limits

- Editable modules use browser storage (per-device). To share data across users, swap the `STORE`
  object in `index.html` for a Supabase/Firebase call — it's isolated in one place.
- Charts and Excel export load from cdnjs at runtime, so the page needs internet access to render
  charts. The data itself is local.
