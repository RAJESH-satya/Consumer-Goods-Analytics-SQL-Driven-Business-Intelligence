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
