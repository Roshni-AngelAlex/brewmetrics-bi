# Copilot-Assisted DAX Development

## Measure 1 — Total Sales

### Copilot's initial suggestion

```DAX
measure 'Total Sales' = SUM(Fact_Sales[sales_amount])
```

### Correction

The DAX expression was correct, but the measure had already been
defined in the Fact_Sales TMDL file. Adding the same measure again
created a duplicate TMDL object and caused Power BI to report:

"TMDL objects cannot be merged because both declare the same property: expression."

The duplicate definition was removed and the original measure was retained.

### Final measure

```DAX
measure 'Total Sales' = SUM(Fact_Sales[sales_amount])
```

---

## Measure 2 — MoM Sales Growth %

### Copilot's initial suggestion

```DAX
MoM Sales Growth % =
VAR CurrentSales = [Total Sales]
VAR PreviousMonthSales =
    CALCULATE(
        [Total Sales],
        DATEADD('Dim_Date'[date], -1, MONTH)
    )
RETURN
    DIVIDE(CurrentSales - PreviousMonthSales, PreviousMonthSales)
```

### Correction

The DAX logic itself was correct on the first try — using `DATEADD` with
`-1, MONTH` instead of `SAMEPERIODLASTYEAR` was the right choice, since the
dataset only spans April–June and a year-over-year comparison would return
blank everywhere. The only issue was adding `formatString: 0.00%` directly
inside the DAX formula bar in Power BI Desktop — that syntax is valid in the
raw `.tmdl` file but not inside the inline formula editor, which caused a
"syntax for 'formatString' is incorrect" error. Fixed by removing that line
from the formula and setting the percentage format through the Measure
Tools ribbon instead.

### Final measure

```DAX
MoM Sales Growth % =
VAR CurrentSales = [Total Sales]
VAR PreviousMonthSales =
    CALCULATE(
        [Total Sales],
        DATEADD('Dim_Date'[date], -1, MONTH)
    )
RETURN
    DIVIDE(CurrentSales - PreviousMonthSales, PreviousMonthSales)
```

---

## Measure 3 — Running Total Sales

### Copilot's initial suggestion

```DAX
Running Total Sales =
CALCULATE(
    [Total Sales],
    ALL('Dim_Date'[date]),
    'Dim_Date'[date] <= MAX('Dim_Date'[date])
)
```

### Correction

No correction was needed. Copilot correctly filtered through the `Dim_Date`
dimension table rather than referencing `Fact_Sales[date]` directly, which
is the right pattern for a running total in a star schema.

### Final measure

```DAX
Running Total Sales =
CALCULATE(
    [Total Sales],
    ALL('Dim_Date'[date]),
    'Dim_Date'[date] <= MAX('Dim_Date'[date])
)
```