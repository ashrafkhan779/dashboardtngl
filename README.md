# Finance Control — Sales & Financial BI Dashboard

A single-file, zero-build web dashboard for a UAE trading business. It reads a cleaned
sales dataset and adds editable finance modules (expenses, banks, assets, cash flow) that
live entirely in your browser — no backend, no database.

---

## Files

| File | What it is |
|---|---|
| `index.html` | The whole application (all tabs, charts, logic). |
| `data.json` | The bundled sales dataset the dashboard loads on first run. |
| `convert.py` | Regenerates `data.json` from an Excel export. |
| `Sales_Data_Template.xlsx` | The editable sales file — update it and re-upload (or feed it to `convert.py`). |
| `README.md` | This file. |

Keep `index.html` and `data.json` in the **same folder**.

---

## Running it

The app loads `data.json` with `fetch`, which browsers block on `file://`. So **serve the
folder** rather than double-clicking `index.html`:

```bash
# from inside the folder
python -m http.server
# then open http://localhost:8000
```

Or push the folder to **GitHub Pages** (Settings → Pages → deploy from branch). The live URL
loads `data.json` automatically.

> Exception: a **Live Dashboard** export (see *Backup & Share*) is fully self-contained and
> *does* open by double-click — data is baked in, no server needed.

Charts are drawn with Chart.js from a CDN, so an internet connection is needed for the graphs
to render. All tables, KPIs and figures work offline.

---

## Updating the data — two ways

### 1. Upload in the app (easiest, no tools)

1. Open `Sales_Data_Template.xlsx`. It already holds your current data.
2. Add new invoices as rows on the **Input Data** sheet. Fill: `Invoice#, Invoice Date,
   Customer Name, Quarter, Month, Years, Mode, Invoice Amount, Cost, Sales Person`.
   **Profit and Gross %** calculate themselves — leave them alone.
3. Save, then click **⭱ Upload Excel** (bottom-left). Every chart, KPI and table refreshes.

The upload is cleaned automatically (duplicate customers merged, dates repaired, profit
recomputed) — the same logic as `convert.py`. Your uploaded data is **saved on that device**;
you do not need to also replace `data.json` for your own live preview.

### 2. Regenerate `data.json` with the script

```bash
pip install pandas numpy openpyxl        # first time only
python convert.py "Your New File.xlsx"   # writes a fresh data.json
```

Commit the new `data.json` and GitHub Pages updates itself. Both the script and the in-app
uploader expect a sheet named **Input Data** with the columns listed above.

---

## Auto-publish to GitHub (live for everyone)

To make the hosted link update automatically — with **no manual `data.json` uploads** and with
your manual figures visible to anyone who opens it — connect the repo once:

1. Open **⇩ Backup / Share ▸ Connect GitHub**.
2. Enter your **owner**, **repository**, **branch**, and a **Personal Access Token**
   (GitHub → Settings → Developer settings → **Fine-grained tokens**, limited to this repo with
   **Contents: Read and write**). The token is stored **only in your browser**.

Once connected:
- **Uploading an Excel or a data.json auto-commits `data.json`** to your repo — the live link
  updates by itself.
- **Manual figures** (Bank Management, Operational Expenses, Cash Flow, Assets, Corporate Tax…)
  auto-commit to **`manual-data.json`** a few seconds after you edit. A footer indicator shows
  “✓ synced” or “● changes not published”, with a **Publish** button for an immediate push.
- **Anyone opening the live link** loads both `data.json` and `manual-data.json`, so they see
  the same sales data **and** manual entries — no re-entering, nothing to send.

> The app reads `manual-data.json` on load if it exists. Your own unpublished edits are kept
> until you publish; visitors always see the last published version.

## Backup & Share (no GitHub needed)

The same **⇩ Backup / Share** panel also offers, whether or not GitHub is connected:

- **Upload data.json** — load a `data.json` backup as the live dataset (and publish it if
  connected).
- **Live Dashboard (single HTML)** — one self-contained file with your sales data **and** all
  manual entries baked in. Send it to anyone; they open it (even by double-click, no server,
  no `data.json`) and see your live data.
- **data.json** — download the current dataset for a manual backup or repo replacement.
- **All Dashboard Data (Excel)** — a backup workbook of every manual entry (banks, assets,
  liabilities, expenses, corporate tax, receivables/payables, upcoming expenses).
- **Sales Data (Excel)** — the cleaned sales dataset as an editable file to re-upload later.

**Manual data persistence:** figures you enter are stored in your browser and persist across
reloads. With GitHub connected they also live in `manual-data.json` in the repo; otherwise move
them with a Live Dashboard export or the Excel backup.

---

## Tabs

**Analytics**
- **Dashboard** — company KPIs (Sales, COGS, Gross Profit, Gross Margin, Invoices, Operating
  Expenses, Net Profit, Net Profit Margin), yearly performance, sales by mode, monthly trend,
  profit bridge (Sales → COGS → Gross Profit → OPEX → CT → Net Profit), Top-20 customers and
  customer concentration.
- **Customer Analysis** — per-customer multi-year comparison (Month & Mode filters).
- **Salesperson Performance** — leaderboard, margin quality, monthly trend.
- **Quarterly Comparison** — Q1–Q4 across selected years, with Sales / Gross Profit / Margin.
- **New Customer Onboard** — customers by first-invoice period (year / quarter / month).
- **Dormant Customers** — who hasn't ordered in 1 month / 2 months / quarter / half-year /
  year, with search and sortable columns.

**Finance**
- **Profit & Loss** — dynamic P&L (revenue & COGS from sales, opex + corporate tax from the
  Expenses module) down to Net Profit after tax, with trend and comparison charts.
- **Fixed Assets & Liabilities** — assets with depreciation, interest and returns (net book
  value); liabilities with payments and returns (outstanding balance).
- **Operational Expenses** — editable year × month matrix across 20 heads, year-wise totals,
  comparison charts, and a Corporate Tax by-year entry that flows into the P&L.
- **Bank Management** — per-account balances with grouped totals (Banks, Petty Cash, Other).
- **Cash Flow Control** — Local/Oversea Receivable & Payable figures, Available Cash, a
  Net + Available Cash position, and month-wise Upcoming Expenses with a chart.

---

## Data model (for reference)

Source sheet **Input Data**, columns: `Invoice#, Invoice Date, Customer Name, Quarter, Month,
Years, Mode, Invoice Amount, Cost, [Profit], [Gross %], Sales Person`.

Cleaning applied by `convert.py` and the in-app uploader:
- Trim names and merge case/spacing-duplicate customers to the most common spelling.
- Repair Excel-serial or text dates to ISO.
- Recompute `Profit = Invoice Amount − Cost` and `Gross % = Profit / Invoice Amount` (zero-safe).

`data.json` is dictionary-encoded (customers / modes / salespeople indexed) with a `meta`
block holding source name, generated timestamp, date range and totals.

---

## Notes

- No figures are invented — every number is computed from the data you provide.
- Nothing is uploaded anywhere; the app runs entirely in your browser.
