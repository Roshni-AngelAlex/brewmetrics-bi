# Copilot-Assisted DAX Development

## Measure 1 — Total Sales

### Copilot's initial suggestion

```DAX
measure 'Total Sales' = SUM(Fact_Sales[sales_amount])

### Correction

The DAX expression was correct, but the measure had already been
defined in the Fact_Sales TMDL file. Adding the same measure again
created a duplicate TMDL object and caused Power BI to report:

"TMDL objects cannot be merged because both declare the same property: expression."

The duplicate definition was removed and the original measure was retained.

## Measure 1 — Total Sales

### Copilot's initial suggestion

```DAX
measure 'Total Sales' = SUM(Fact_Sales[sales_amount])