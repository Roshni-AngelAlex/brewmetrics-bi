# BrewMetrics BI

A version-controlled Power BI analytics solution for BrewMetrics Coffee Co. The report analyzes sales across products, cities, store formats, and dates, with reusable DAX measures for total sales, growth, ranking, and mix analysis.

## Project Overview

This repository contains a Power BI Project (`.pbip`) with:

- A semantic model defined in TMDL.
- A report definition containing the dashboard pages and visuals.
- Source-driven Power Query partitions for the sales and dimension tables.
- DAX measures including `Total Sales`, `MoM Sales Growth %`, `Running Total Sales`, `City Sales Rank`, and `Cold Brew Share of Coffee %`.

The model currently covers sales from April through July 2026.

## Star Schema

The model uses `Fact_Sales` as the central fact table and dimensions for analysis:

```text
                    Dim_Date
                       |
Dim_City ------ Fact_Sales ------ Dim_Product
                       |
                Dim_StoreFormat
```

### Fact_Sales

The transaction-level table. It contains the sale date, city, store format, product category, item, quantity, unit price, sales amount, and product key. Measures such as `Total Sales` aggregate the `sales_amount` column.

### Dim_Date

The date dimension used for time intelligence, including year, month number, and month name. It filters `Fact_Sales` through the sales date and supports month-over-month and running-total calculations.

### Dim_City

The city dimension used to compare sales performance across Bengaluru, Chennai, Hyderabad, and Coimbatore.

### Dim_Product

The product dimension containing product category, item, and product key attributes. It drives the Coffee Item Breakdown table and product-level analysis.

`Dim_StoreFormat` is also included as a supporting dimension for store-format analysis.

## Dashboard Insights

- **Cold Brew is seasonal:** Cold Brew sales spike in April and May, reaching approximately 276.6K in April and 301.3K in May before declining in June and July.
- **Bengaluru leads city sales:** Bengaluru is the top-performing city at approximately 1.12M in sales, ahead of Chennai, Hyderabad, and Coimbatore.
- **Cold Brew leads the Coffee Item Breakdown:** Cold Brew is the highest-selling Coffee item at approximately 771.3K, representing about 42.7% of Coffee category sales. It leads Cappuccino, Espresso, and Filter Coffee.

## Repository Structure


- `BrewMetrics.pbip` - Power BI Project entry point.
- `BrewMetrics.Report/` - Report pages, visuals, and shared resources.
- `BrewMetrics.SemanticModel/` - TMDL model, tables, relationships, and measures.
- `README.md` - Project documentation.
- `NOTES.md` - Copilot-assisted DAX development log.
- `REFLECTION.md` - Reflection on the AI-assisted, version-controlled workflow.
- `BrewMetrics_Dashboard.pdf` - PDF export of the final dashboard.

