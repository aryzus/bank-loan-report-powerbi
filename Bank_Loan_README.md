# 🏦 Bank Loan Report — Power BI Dashboard

A three-dashboard Power BI report analyzing a bank's lending portfolio — covering KPI tracking, visual trend analysis, and a detailed loan-level drill-through view.

---

## 📌 Overview

This project builds a comprehensive Bank Loan Report using **Power BI** and **DAX**. It tracks loan application volumes, funded amounts, repayments, interest rates, and borrower risk profiles — enabling data-driven decisions across a lending portfolio.

The report is split into three purpose-built dashboards:

| Dashboard | Purpose |
|---|---|
| **Summary** | High-level KPIs, Good vs Bad loan breakdown, loan status grid |
| **Overview** | Visual trends across time, geography, purpose, term, and ownership |
| **Details** | Row-level loan data with multi-dimensional slicers |

---

## 📊 Dashboard Breakdown

### Dashboard 1 — Summary
Tracks the core health metrics of the loan portfolio:

- **Total Loan Applications** with MTD and MoM change
- **Total Funded Amount** with MTD and MoM change
- **Total Amount Received** with MTD and MoM change
- **Average Interest Rate** (portfolio-wide and MTD)
- **Average Debt-to-Income Ratio (DTI)** (portfolio-wide and MTD)
- **Good Loan vs Bad Loan KPIs** — application count, funded amount, amount received, and percentage split
- **Loan Status Grid** — summary table by loan status with all key metrics

### Dashboard 2 — Overview
Six visual breakdowns of lending activity:

- 📈 **Monthly Trend Line** — Applications, Funded, Received by month
- 🗺️ **State Filled Map** — Funded amount by US state
- 🍩 **Loan Term Donut** — 36-month vs 60-month split (73.2% vs 26.8%)
- 📊 **Employment Length Bar** — Applications by borrower employment tenure
- 📊 **Loan Purpose Bar** — Debt consolidation, credit card, home improvement, etc.
- 🟦 **Home Ownership Treemap** — Rent vs Mortgage vs Own

### Dashboard 3 — Details
Full loan-level table with slicers for:
- Month/Year, Address State, Loan Status, Purpose
- Employment Length, Loan Quality (Good/Bad), Issue Date

---

## 🗂️ Project Structure

```
Bank-Loan-Report/
│
├── bank_loan_report.pbix       # Power BI report file
├── financial_loan.csv          # Source dataset (not included)
├── DAX_measures.md             # All DAX measures documented & corrected
├── README.md                   # Project documentation
│
└── screenshots/
    ├── dashboard_summary.png
    ├── dashboard_overview.png
    └── dashboard_details.png
```

---

## 🛠️ Tools & Techniques

- **Power BI Desktop** — report design and publishing
- **DAX** — all KPIs built from scratch using calculated measures
- **Power Query** — data type formatting, date table creation
- **Date Table** — custom calendar table for MTD/MoM/YTD time intelligence

---

## ⚙️ DAX Concepts Used

| Concept | Example Measure |
|---|---|
| `CALCULATE` + `TOTALMTD` | MTD Loan Applications |
| `DATESMTD` + `DATEADD` | PMTD (Previous Month-to-Date) |
| `DIVIDE` | Safe MoM % change |
| Conditional filtering | Good Loan vs Bad Loan classification |
| `AVERAGE` with time intelligence | MTD Avg Interest Rate, MTD Avg DTI |

> See `DAX_measures.md` for all measures with corrections and formatting fixes.

---

## 🔍 Key Insights from the Data

- **86%** of loans are classified as Good Loans (Fully Paid or Current)
- **Debt Consolidation** is by far the most common loan purpose (~18K applications)
- **10+ years employment** borrowers have the highest application volume (8.9K)
- **73.2%** of loans are on 36-month terms vs 26.8% on 60-month terms
- **Renters and mortgage holders** account for nearly all applications (18K + 17K vs 3K owners)
- Total Funded: **$436M** | Total Received: **$473M** — healthy repayment surplus

---

## 🚀 How to Open

1. Download and install [Power BI Desktop](https://powerbi.microsoft.com/desktop/)
2. Clone or download this repository
3. Place `financial_loan.csv` in the project root
4. Open `bank_loan_report.pbix`
5. Go to **Transform Data → Data Source Settings** and update the CSV path
6. Click **Refresh All**

---

## 🔍 Key Concepts Practiced

- Multi-page dashboard design with consistent theme
- Time intelligence in DAX (`TOTALMTD`, `DATEADD`, `DATESMTD`)
- KPI card design with MoM variance indicators
- Filled map, treemap, donut, and bar chart configuration
- Slicer-driven cross-filtering across all visuals
- Good/Bad loan segmentation using calculated columns

---

*Built to practice advanced Power BI reporting and DAX time intelligence 📊*
