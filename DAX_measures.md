# 📐 DAX Measures — Bank Loan Report
All measures used in the report, with corrections for known display issues.

---

## ⚠️ Known Issues & Fixes

| Issue Observed | Root Cause | Fix |
|---|---|---|
| `Avg Int Rate = 0.12` showing as decimal | Column `int_rate` stored as decimal (0.12), not percentage | Format measure as **Percentage** in Power BI |
| `Good Loan % = 0.86` showing as decimal | Same — measure returns 0–1 range | Format measure as **Percentage** |
| `PMTD Applications = --` blank | Date table not marked as Date Table, or relationship inactive | Mark Date Table → **Table Tools → Mark as Date Table** |
| `MTD Avg DTI = --` blank | Same date table issue | Same fix as above |
| Date slicer showing `01-01-2021 00:00:00` | Date column includes time component | In Power Query: change column type to **Date** (not DateTime) |

---

## 1. 📋 Total Applications & MoM

```dax
-- Total loan applications
Total Loan Applications =
    COUNT(financial_loan[id])

-- Month-to-date applications
MTD Applications =
    CALCULATE(
        TOTALMTD(COUNT(financial_loan[id]), 'Date Table'[Date])
    )

-- Previous month-to-date applications
PMTD Loan Applications =
    CALCULATE(
        [Total Loan Applications],
        DATESMTD(DATEADD('Date Table'[Date], -1, MONTH))
    )

-- Month-over-month change (formatted as %)
MoM Loan Applications =
    DIVIDE(
        [MTD Applications] - [PMTD Loan Applications],
        [PMTD Loan Applications],
        0
    )
```

> 💡 Use `DIVIDE()` instead of `/` to safely handle divide-by-zero (returns 0 instead of error).

---

## 2. 💰 Funded Amount & MoM

```dax
Total Funded Amount =
    SUM(financial_loan[loan_amount])

MTD Funded Amount =
    CALCULATE(
        TOTALMTD(SUM(financial_loan[loan_amount]), 'Date Table'[Date])
    )

PMTD Funded Amount =
    CALCULATE(
        SUM(financial_loan[loan_amount]),
        DATESMTD(DATEADD('Date Table'[Date], -1, MONTH))
    )

MoM Funded Amount =
    DIVIDE(
        [MTD Funded Amount] - [PMTD Funded Amount],
        [PMTD Funded Amount],
        0
    )
```

---

## 3. 💵 Amount Received & MoM

```dax
Total Amount Received =
    SUM(financial_loan[total_payment])

MTD Amount Received =
    CALCULATE(
        TOTALMTD(SUM(financial_loan[total_payment]), 'Date Table'[Date])
    )

PMTD Amount Received =
    CALCULATE(
        SUM(financial_loan[total_payment]),
        DATESMTD(DATEADD('Date Table'[Date], -1, MONTH))
    )

MoM Amount Received =
    DIVIDE(
        [MTD Amount Received] - [PMTD Amount Received],
        [PMTD Amount Received],
        0
    )
```

---

## 4. 📈 Average Interest Rate & MoM

```dax
-- ✅ FIXED: Format this measure as "Percentage" in Power BI
-- (Modeling tab → Format → Percentage) so 0.12 displays as 12%
Avg Interest Rate =
    AVERAGE(financial_loan[int_rate])

MTD Avg Interest Rate =
    CALCULATE(
        TOTALMTD(AVERAGE(financial_loan[int_rate]), 'Date Table'[Date])
    )

PMTD Avg Interest Rate =
    CALCULATE(
        AVERAGE(financial_loan[int_rate]),
        DATESMTD(DATEADD('Date Table'[Date], -1, MONTH))
    )

MoM Avg Interest Rate =
    DIVIDE(
        [MTD Avg Interest Rate] - [PMTD Avg Interest Rate],
        [PMTD Avg Interest Rate],
        0
    )
```

---

## 5. 📊 Average DTI & MoM

```dax
-- ✅ FIXED: Format as Percentage so 0.133 displays as 13.3%
Avg DTI =
    AVERAGE(financial_loan[dti])

MTD Avg DTI =
    CALCULATE(
        TOTALMTD(AVERAGE(financial_loan[dti]), 'Date Table'[Date])
    )

PMTD Avg DTI =
    CALCULATE(
        AVERAGE(financial_loan[dti]),
        DATESMTD(DATEADD('Date Table'[Date], -1, MONTH))
    )

MoM Avg DTI =
    DIVIDE(
        [MTD Avg DTI] - [PMTD Avg DTI],
        [PMTD Avg DTI],
        0
    )
```

---

## 6. ✅ Good Loan KPIs

```dax
Good Loan Applications =
    CALCULATE(
        [Total Loan Applications],
        financial_loan[Good v Bad Loan] = "Good Loan"
    )

-- ✅ FIXED: Use DIVIDE and format as Percentage → displays as 86% not 0.86
Good Loan % =
    DIVIDE(
        CALCULATE(
            [Total Loan Applications],
            financial_loan[Good v Bad Loan] = "Good Loan"
        ),
        [Total Loan Applications],
        0
    )

Good Loan Funded Amount =
    CALCULATE(
        [Total Funded Amount],
        financial_loan[Good v Bad Loan] = "Good Loan"
    )

Good Loan Total Received =
    CALCULATE(
        [Total Amount Received],
        financial_loan[Good v Bad Loan] = "Good Loan"
    )
```

---

## 7. ❌ Bad Loan KPIs

```dax
Bad Loan Applications =
    CALCULATE(
        [Total Loan Applications],
        financial_loan[Good v Bad Loan] = "Bad Loan"
    )

-- ✅ FIXED: Use DIVIDE and format as Percentage
Bad Loan % =
    DIVIDE(
        [Bad Loan Applications],
        [Total Loan Applications],
        0
    )

Bad Loan Funded Amount =
    CALCULATE(
        [Total Funded Amount],
        financial_loan[Good v Bad Loan] = "Bad Loan"
    )

Bad Loan Total Received =
    CALCULATE(
        [Total Amount Received],
        financial_loan[Good v Bad Loan] = "Bad Loan"
    )
```

---

## 8. 🗓️ Calculated Column — Good v Bad Loan

```dax
-- Add this as a Calculated Column on the financial_loan table
Good v Bad Loan =
    IF(
        financial_loan[loan_status] IN { "Fully Paid", "Current" },
        "Good Loan",
        "Bad Loan"
    )
```

---

## 9. 📅 Date Table (Power Query M)

If your MTD/PMTD measures are returning blanks (`--`), your Date Table may be missing or disconnected. Create it in Power Query:

```m
let
    StartDate = #date(2021, 1, 1),
    EndDate = #date(2021, 12, 31),
    Duration = Duration.Days(EndDate - StartDate) + 1,
    Dates = List.Dates(StartDate, Duration, #duration(1, 0, 0, 0)),
    Table = Table.FromList(Dates, Splitter.SplitByNothing(), {"Date"}),
    ChangedType = Table.TransformColumnTypes(Table, {{"Date", type date}}),
    AddYear = Table.AddColumn(ChangedType, "Year", each Date.Year([Date]), Int64.Type),
    AddMonth = Table.AddColumn(AddYear, "Month", each Date.Month([Date]), Int64.Type),
    AddMonthName = Table.AddColumn(AddMonth, "MonthName", each Date.ToText([Date], "MMM"), type text),
    AddMonthYear = Table.AddColumn(AddMonthName, "MonthYear", each Date.ToText([Date], "MMM yyyy"), type text)
in
    AddMonthYear
```

Then:
1. Go to **Table Tools → Mark as Date Table → Date**
2. Create a relationship: `Date Table[Date]` → `financial_loan[issue_date]`
3. Refresh — MTD/PMTD blanks will resolve ✅

---

## 🔧 Power BI Formatting Checklist

| Measure | Format Setting | Result |
|---|---|---|
| Avg Interest Rate | Percentage, 2 decimal | `12.00%` |
| Good Loan % | Percentage, 1 decimal | `86.2%` |
| Bad Loan % | Percentage, 1 decimal | `13.8%` |
| Avg DTI | Percentage, 1 decimal | `13.3%` |
| MoM measures | Percentage, 2 decimal | `+4.23%` |
| Total Funded Amount | Currency ($), 0 decimal | `$436M` |
| Date slicer column | Date (not DateTime) | `01 Jan 2021` |
