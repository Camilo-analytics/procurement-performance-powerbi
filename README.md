# Procurement Performance Dashboard — Power BI

## Project Overview

End-to-end Power BI project analysing construction procurement performance for a fictional Australian building materials distributor. The analysis covers **2,000 purchase orders** across **25 suppliers** and **6 product categories** from **January 2024 to April 15, 2026**.

The dashboard was built to answer a real business problem: leadership had no visibility into whether suppliers were delivering on time, where the budget was going, and whether procurement costs were growing year on year.

---

## Dashboard Preview

> *(Add screenshots of each page here)*

| Page | Description |
|------|-------------|
| Executive Summary | KPI overview + monthly spend trend |
| Supplier Performance | On-time rate and spend by supplier and tier |
| Spend by Category | Budget distribution across 6 product categories |
| YoY Spend Analysis | Year-over-year spend comparison 2024 vs 2025 vs 2026 |

---

## Business Questions Answered

1. **How is overall procurement performing?** — Total spend, order volume, on-time rate, and average lead time at a glance
2. **Which suppliers are failing to deliver on time?** — On-time rate ranked by supplier, segmented by tier (A/B/C)
3. **Where is the budget going?** — Spend distribution across Structural, Electrical, Concrete, Plumbing, Finishes, and Safety categories
4. **Are we spending more than last year?** — Year-over-year spend comparison by month and total

---

## Key Findings

**1. Only 56.5% of orders are delivered on time**
With an average lead time of 12.9 days, nearly half of all purchase orders arrive late. This represents a significant operational risk across the supply chain.

**2. Tier C suppliers are the highest delivery risk**
Global Electrical Imports (CN, Tier C) recorded the lowest on-time rate at 21.3%, followed by Asia Pacific Cement (CN, Tier C) at 24.5%. Tier C suppliers consistently fall below 30% on-time delivery — more than 30 percentage points below Tier A suppliers who average above 65%.

**3. Procurement spend increased 68.5% year over year**
Total spend grew significantly from 2024 to 2025. Structural and Electrical categories together account for over 50% of total spend, making them the highest-priority categories for cost control.

---

## Data Quality Findings

During the Power Query cleaning phase, the following data quality issues were identified and documented:

| Issue | Count | Action Taken |
|-------|-------|--------------|
| Mixed date formats (5 formats) | ~15% of records | Parsed to ISO date using custom M formula |
| Negative unit prices | 43 records | Converted to absolute value |
| Zero quantity orders (status: Delivered) | 23 records | Retained — flagged as data entry anomaly |
| Blank supplier_id | 24 records | Retained as blank — excluded from supplier analysis |
| Duplicate suppliers (same name, different ID and tier) | 4 pairs | Flagged with `data_quality_flag = "Duplicate — review required"` |
| Duplicate products (same name, different ID) | 7 pairs | Flagged with `data_quality_flag = "Duplicate — review required"` |

> Note: 2026 data covers January 1 to April 15 only. Q1 and Q2 2026 figures are partial and not directly comparable to full-year quarters.

---

## Technical Stack

| Tool | Usage |
|------|-------|
| Power BI Desktop | Data modelling, DAX measures, dashboard |
| Power Query (M) | Data cleaning and transformation |
| DAX | Calculated measures including time intelligence |
| GitHub | Version control and portfolio publishing |

---

## Data Model — Star Schema

```
fact_orders (2,000 rows)
    ├── dim_suppliers (25 rows)   → supplier_id
    ├── dim_products (40 rows)    → product_id
    └── dim_dates (836 rows)      → order_date
```

**Relationships:** All Many-to-One (*:1), single cross-filter direction from dimension to fact table.

---

## DAX Measures

```dax
Total Spend = SUM(fact_orders[total_amount])

Total Orders = COUNTROWS(fact_orders)

On-Time Rate % =
DIVIDE(
    COUNTROWS(FILTER(fact_orders, fact_orders[on_time_flag] = 1)),
    COUNTROWS(FILTER(fact_orders, fact_orders[status] = "Delivered")),
    0
)

Avg Lead Time (days) =
AVERAGEX(
    FILTER(fact_orders, fact_orders[actual_lead_days] <> BLANK()),
    fact_orders[actual_lead_days]
)

YoY Spend % =
DIVIDE(
    SUM(fact_orders[total_amount]) - CALCULATE(SUM(fact_orders[total_amount]), SAMEPERIODLASTYEAR(dim_dates[date])),
    CALCULATE(SUM(fact_orders[total_amount]), SAMEPERIODLASTYEAR(dim_dates[date])),
    BLANK()
)
```

---

## Dataset

| File | Rows | Description |
|------|------|-------------|
| `fact_orders.csv` | 2,000 | Purchase orders with dates, quantities, prices, delivery status |
| `dim_suppliers.csv` | 25 | Supplier master data with tier, country, and category |
| `dim_products.csv` | 40 | Product catalogue with category and standard cost |
| `dim_dates.csv` | 836 | Date dimension covering Jan 2024 to Apr 15, 2026 |

Dataset is synthetic and generated for portfolio purposes. Supplier and product names reference real Australian construction industry companies for realism.

---

## Project Pipeline

| Phase | Description |
|-------|-------------|
| 1 — Design | Business problem definition, star schema design |
| 2 — Data generation | Synthetic dataset with deliberate data quality issues |
| 3 — Power Query | Data cleaning, type conversion, date parsing, quality flags |
| 4 — Data model | Star schema relationships, DAX measures |
| 5 — Dashboard | 4-page Power BI report with cross-filtering |
| 6 — Publishing | GitHub documentation and portfolio publication |

---

## Author

**Camilo Barrera Martinez**
Data Analyst | Adelaide, SA, Australia
[LinkedIn](https://www.linkedin.com/in/ccbmoz19) · [GitHub](https://github.com/Camilo-analytics)
