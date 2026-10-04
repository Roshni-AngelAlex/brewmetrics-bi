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
I’ll verify the current measure block and the city dimension column name, then add the `RANKX` measure using the existing `[Total Sales]` measure.

Read , lines 1 to 45

Read 

`Dim_City[city]` is the correct grouping column, and `[Total Sales]` is available in the same table. I’ll add a dense descending rank over all cities, which gives the highest-selling city rank 1 and keeps other report filters active.

Generating patch (17 lines) in 


```

```
The `City Sales Rank` measure is added with descending dense ranking over `Dim_City[city]`. I’m running the focused TMDL error check and confirming the `RANKX` expression is present.

Checked , no problems found

Searched for regex `measure 'City Sales Rank'|RANKX\(|ALL\('Dim_City'\[city\]\)|DESC|DENSE` (`**/BrewMetrics.SemanticModel/definition/tables/Fact_Sales.tmdl`), 5 results

Added `City Sales Rank` to `Fact_Sales.tmdl:30` using `RANKX`, descending order, and dense ranking. Rank 1 represents the highest-selling city.

Validation completed with no errors.

Made changes.

I’ll verify the current `Fact_Sales` measure block and the exact category/item column names, then add the share measure using `[Total Sales]` with a Coffee-only denominator and Cold Brew numerator.

Searched for regex `column category|column item|measure 'Total Sales'` (`**/BrewMetrics.SemanticModel/definition/tables/*.tmdl`), 5 results

Read , lines 1 to 55

Read , lines 1 to 40

The model exposes `Fact_Sales[category]` and `Fact_Sales[item]` directly, so I’ll use those fields to avoid relying on the model’s limited product relationship. The denominator will clear item filtering while fixing category to Coffee; the numerator will additionally fix item to Cold Brew.

Generating patch (24 lines) in 


```

```
The percentage measure is added with the requested numerator and denominator logic and an explicit percent format. I’m validating the edited TMDL and confirming the measure name plus both category/item filters.

Checked , no problems found

Searched for regex `measure 'Cold Brew Share of Coffee %'|\[category\] = "Coffee"|\[item\] = "Cold Brew"|REMOVEFILTERS\('Fact_Sales'\[item\]\)` (`**/BrewMetrics.SemanticModel/definition/tables/Fact_Sales.tmdl`), 5 results

Added `Cold Brew Share of Coffee %` to `Fact_Sales.tmdl:39`. It calculates Cold Brew sales divided by total Coffee sales using `[Total Sales]`, formatted as a percentage.

Validation completed with no errors.

Made changes.