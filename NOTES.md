# NOTES.md — Copilot-Assisted DAX Development

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
dataset only spans a few months and a year-over-year comparison would
return blank everywhere. The only issue was adding `formatString: 0.00%`
directly inside the DAX formula bar in Power BI Desktop — that syntax is
valid in the raw `.tmdl` file but not inside the inline formula editor,
which caused a "syntax for 'formatString' is incorrect" error. Fixed by
removing that line from the formula and setting the percentage format
through the Measure Tools ribbon instead.

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

---

## Measure 4 — City Sales Rank

### Copilot's initial suggestion

```DAX
City Sales Rank =
RANKX(
    ALL('Dim_City'[city]),
    [Total Sales],
    ,
    DESC,
    DENSE
)
```

### Correction

No correction was needed. Copilot correctly wrapped the ranking table in
`ALL('Dim_City'[city])`, which keeps the rank accurate even when a city
slicer filters the report down to one city — without `ALL()`, a filtered
city would incorrectly always show as Rank 1. Copilot also used `DENSE`
ranking rather than the default skip-rank behavior; with only 4 cities and
no tied totals in this dataset, the two approaches produce the same result
here, but `DENSE` is a deliberate choice worth noting.

### Final measure

```DAX
City Sales Rank =
RANKX(
    ALL('Dim_City'[city]),
    [Total Sales],
    ,
    DESC,
    DENSE
)
```

---

## Measure 5 — Cold Brew Share of Coffee % (own choice)

### Copilot's initial suggestion

```DAX
Cold Brew Share of Coffee % =
DIVIDE(
    CALCULATE(
        [Total Sales],
        'Fact_Sales'[category] = "Coffee",
        'Fact_Sales'[item] = "Cold Brew"
    ),
    CALCULATE(
        [Total Sales],
        'Fact_Sales'[category] = "Coffee",
        REMOVEFILTERS('Fact_Sales'[item])
    )
)
```

### Correction

The division logic and `DIVIDE()` safety were correct on the first try.
One recurring formatting issue appeared again here: `formatString` was
written directly into the DAX formula bar, which Power BI rejects as a
syntax error in that context — this was the second time the same mistake
occurred (also seen in Measure 2), fixed the same way, via the ribbon.

A design choice worth noting: Copilot filtered on `Fact_Sales[category]`
and `Fact_Sales[item]` directly, rather than through the `Dim_Product`
dimension table used elsewhere in the model. This works because
`Fact_Sales` retains its own `category`/`item` columns alongside the
`ProductKey` that links to `Dim_Product`, and it sidesteps any uncertainty
about how that relationship propagates filters.

### Final measure

```DAX
Cold Brew Share of Coffee % =
DIVIDE(
    CALCULATE(
        [Total Sales],
        'Fact_Sales'[category] = "Coffee",
        'Fact_Sales'[item] = "Cold Brew"
    ),
    CALCULATE(
        [Total Sales],
        'Fact_Sales'[category] = "Coffee",
        REMOVEFILTERS('Fact_Sales'[item])
    )
)
```