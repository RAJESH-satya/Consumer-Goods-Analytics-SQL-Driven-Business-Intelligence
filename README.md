# Consumer-Goods-Analytics-SQL-Driven-Business-Intelligence
SQL-powered analytics project in the consumer goods domain. Delivered actionable insights on sales, customer behavior, product trends, and market performance using joins, CTEs, and window functions—crafted for executive-level decision-making.

## Table of Contents

- [Data Sources](#data-sources)
- [Techniques Implemented](#techniques-implemented)
- [Business Insights Covered](#business-insights-covered)
  - [1. Market Presence by Region](#1-market-presence-by-region)
  - [2. Product Growth Analysis (2020 vs 2021)](#2-product-growth-analysis-2020-vs-2021)
  - [3. Segment-wise Product Distribution](#3-segment-wise-product-distribution)
  - [4. Segment Growth Insights](#4-segment-growth-insights)
  - [5. Cost Extremes in Manufacturing](#5-cost-extremes-in-manufacturing)
  - [6. Top Customers by Discount Advantage](#6-top-customers-by-discount-advantage)
  - [7. Monthly Sales Trend Report](#7-monthly-sales-trend-report)
  - [8. Best-Performing Quarter](#8-best-performing-quarter)
  - [9. Channel-wise Sales Contribution](#9-channel-wise-sales-contribution)
  - [10. Top-Selling Products by Division](#10-top-selling-products-by-division)
- [Dashboard Overview](#dashboard-overview)
- [Key Learnings](#key-learnings)

## 1. Market Presence by Region  
**Objective**: Identify in which APAC markets the customer **“Atliq Exclusive”** operates.

**Query Techniques**:  
- `SELECT DISTINCT`  
- Filtering with `WHERE`

**SQL Query**:
```sql
SELECT DISTINCT market
FROM dim_customer
WHERE region = "APAC" AND customer = "Atliq Exclusive";
```

## 2. Product Growth Analysis (2020 vs 2021)  
**Objective**: Measure the increase in unique products sold from 2020 to 2021.

**Query Techniques**:  
- Subqueries  
- `COUNT(DISTINCT)`  
- CTE (Common Table Expression)  
- Arithmetic calculation

**SQL Query**:
```sql
WITH cte AS (
  SELECT
    (SELECT COUNT(DISTINCT p.product_code)
     FROM dim_product p
     JOIN fact_gross_price g ON p.product_code = g.product_code
     WHERE fiscal_year = 2020) AS unique_products_2020,
    (SELECT COUNT(DISTINCT p.product_code)
     FROM dim_product p
     JOIN fact_gross_price g ON p.product_code = g.product_code
     WHERE fiscal_year = 2021) AS unique_products_2021
)
SELECT *,
  ROUND(((unique_products_2021 - unique_products_2020) * 100 / unique_products_2020), 2) AS percentage_chg
FROM cte;
```
## 3. Segment-wise Product Distribution
**Objective**: Provide a report of all unique product counts for each segment, sorted by descending order of product counts.

**Query Techniques**:
- Using `COUNT DISTINCT` to find unique products.
- `GROUP BY` to aggregate product counts by segment.
- `ORDER BY` to sort segments by product count in descending order.

```sql
SELECT segment, COUNT(DISTINCT product_code) AS product_count
FROM dim_product
GROUP BY segment
ORDER BY product_count DESC;

