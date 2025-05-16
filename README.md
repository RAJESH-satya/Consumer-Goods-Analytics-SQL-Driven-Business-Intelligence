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

## Data Sources

- **SQL Dataset**:  
  The dataset was provided in the form of SQL table definitions and data inserts. All queries were written using this dataset structure.  
  You can find the complete SQL schema and sample data in the [`data.sql`](./data.sql) file included in this repository.
## Techniques Implemented

- **Data Extraction Using SQL**:  
  Retrieved relevant information using `SELECT`, `DISTINCT`, `WHERE`, and filtering across dimensions like customer, product, and region.

- **Complex Joins**:  
  Utilized `INNER JOIN`, `LEFT JOIN`, and `USING` clause to combine data across multiple tables.

- **Common Table Expressions (CTEs)**:  
  Applied `WITH` clauses to simplify subqueries and structure multi-step analysis cleanly.

- **Aggregation & Grouping**:  
  Used `COUNT`, `SUM`, `ROUND`, and `GROUP BY` to summarize and compare sales and product data effectively.

- **Window Functions**:  
  Implemented `ROW_NUMBER()` and `OVER(PARTITION BY...)` to rank top-performing products within each division.

- **Conditional Logic**:  
  Built quarter-based reports using `CASE WHEN` for logical bucketing of months.

- **Performance Metrics Calculation**:  
  Derived growth percentages, average discounts, and sales contribution percentages for business impact evaluation.

- **Business-Friendly Formatting**:  
  Rounded and formatted large numbers (e.g., millions) for presentation to stakeholders.

### 1. Market Presence by Region  
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
**Insight**:  
The query identifies the specific APAC markets where "Atliq Exclusive" operates, providing a clear geographical scope for business operations in the region. This insight can help prioritize marketing and expansion efforts in key markets.

### 2. Product Growth Analysis (2020 vs 2021)  
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
![image](https://github.com/user-attachments/assets/a28ee1db-a74a-46af-8962-52c6bea3962f)

**Insight**:  
The percentage change in unique products between 2020 and 2021 highlights the product innovation and expansion efforts of the business. A positive growth rate suggests an increase in product diversification, which could lead to new market opportunities.

### 3. Segment-wise Product Distribution
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
```
**Insight**:  
By identifying which segments carry the most products, this insight enables targeted business strategies. It informs inventory management, sales strategies, and potential opportunities for product bundling within the highest-performing segments.

### 4. Segment Growth Insights
**Objective**: Identify which segment had the most increase in unique products in 2021 compared to 2020.

**Query Techniques**:
- Using Common Table Expressions (CTEs) for comparing product counts across years.
- `JOIN` to combine data for 2020 and 2021.
- Calculating differences using simple subtraction.

```sql
WITH cte1 AS (
    SELECT p.segment, COUNT(DISTINCT p.product_code) AS product_count_2020
    FROM dim_product p
    LEFT JOIN fact_gross_price g
    USING (product_code)
    WHERE g.fiscal_year = 2020
    GROUP BY segment
),
cte2 AS (
    SELECT p.segment, COUNT(DISTINCT p.product_code) AS product_count_2021
    FROM dim_product p
    LEFT JOIN fact_gross_price g
    USING (product_code)
    WHERE g.fiscal_year = 2021
    GROUP BY segment
)
SELECT *, (product_count_2021 - product_count_2020) AS Difference
FROM cte1
JOIN cte2
USING (segment)
ORDER BY Difference DESC;
```
**Insight**:  
This analysis uncovers which segments experienced the most significant growth in terms of unique products between 2020 and 2021. Understanding segment growth helps in focusing resources on high-growth areas, improving product offerings, and making strategic decisions about future investments.

### 5. Cost Extremes in Manufacturing
**Objective**: Identify products with the highest and lowest manufacturing costs.

**Query Techniques**:
- `JOIN` to link product and manufacturing cost data.
- Use of subqueries to identify the maximum and minimum manufacturing costs.
- Sorting to highlight cost extremes.

```sql
SELECT p.product_code, p.product, m.manufacturing_cost
FROM dim_product p
JOIN fact_manufacturing_cost m
USING (product_code)
WHERE m.manufacturing_cost IN (
    (SELECT MAX(manufacturing_cost) FROM fact_manufacturing_cost),
    (SELECT MIN(manufacturing_cost) FROM fact_manufacturing_cost)
)
ORDER BY m.manufacturing_cost DESC;
```
**Insight**:  
By identifying products with the highest and lowest manufacturing costs, this query reveals potential areas for cost optimization. Analyzing cost extremes can lead to better pricing strategies and more efficient resource allocation in manufacturing.

### 6. Top Customers by Discount Advantage
**Objective**: Identify the top 5 customers in India with the highest average pre-invoice discount in FY 2021.

**Query Techniques**:
- `JOIN` to merge customer and invoice data.
- `WHERE` to filter by fiscal year and market.
- Sorting and limiting results to get top 5 customers.

```sql
SELECT customer_code, customer, pre_invoice_discount_pct
FROM dim_customer c
JOIN fact_pre_invoice_deductions i
USING (customer_code)
WHERE fiscal_year = 2021 AND market = "india"
ORDER BY pre_invoice_discount_pct DESC
LIMIT 5;
```
**Insight**:  
Identifying the top customers who benefit from the highest pre-invoice discounts offers valuable insights into customer relationships. This information helps in making informed decisions about future pricing, customer loyalty programs, and the long-term value of these customers.

### 7. Monthly Sales Trend Report
**Objective**: Generate a report showing the monthly gross sales for "Atliq Exclusive" to identify low and high-performing months, aiding in strategic decision-making.

**Query Techniques**:
- Use of `WITH` clause to create a Common Table Expression (CTE) for month extraction.
- `JOIN` operations across sales, product, and customer data.
- Aggregating sales data by month and fiscal year.
- Calculating gross sales in millions for a clear and digestible representation.

```sql
WITH cte AS (
    SELECT *, MONTH(DATE_ADD(date, INTERVAL 4 MONTH)) AS month
    FROM gdb023.fact_sales_monthly
)
SELECT s.month, s.fiscal_year, 
       ROUND((SUM((s.sold_quantity * g.gross_price) / 1000000)), 2) AS gross_sales_mln
FROM cte s
JOIN fact_gross_price g USING (product_code)
JOIN dim_customer c USING (customer_code)
WHERE customer = "Atliq Exclusive"
GROUP BY s.month, s.fiscal_year;
```
**Insight**:  
This report on monthly gross sales allows the company to spot seasonal trends, recognize high-sales periods, and adjust sales and marketing strategies accordingly. Understanding the monthly variation in sales helps in improving forecasting accuracy and business planning.

### 8. Best-Performing Quarter
**Objective**: Determine which quarter of the fiscal year 2020 had the highest total sold quantity, aiding in strategic planning and inventory forecasting.

**Query Techniques**:
- Use of `CASE` statement to categorize months into quarters.
- Aggregation of sold quantities by quarter.
- Filtering for the fiscal year 2020.
- Sorting the results by total sold quantity in descending order.

```sql
WITH cte AS (
    SELECT MONTH(DATE_ADD(date, INTERVAL 4 MONTH)) AS month, sold_quantity, fiscal_year
    FROM fact_sales_monthly
)
SELECT CASE
            WHEN month IN (1, 2, 3) THEN 'Q1'
            WHEN month IN (4, 5, 6) THEN 'Q2'
            WHEN month IN (7, 8, 9) THEN 'Q3'
            ELSE 'Q4'
        END AS Quarters,
       ROUND((SUM(sold_quantity) / 1000000), 2) AS_
```
**Insight**:  
By identifying the quarter with the highest sold quantity in 2020, this analysis helps in determining the peak performance period. Businesses can then plan promotions, product launches, and stock allocation based on historical peak periods.

### 9. Channel-wise Sales Contribution
**Objective**: Analyze the sales contribution by different sales channels for the fiscal year 2021, helping identify the most effective sales channels for driving revenue.

**Query Techniques**:
- Aggregating sales by channel using `SUM()`.
- Calculating the percentage contribution of each channel to the total sales.
- Use of `WITH` clause to simplify query structure and break it into manageable parts.

```sql
WITH cte AS (
    SELECT channel, 
           ROUND(SUM((sold_quantity * gross_price) / 100000), 2) AS gross_sales_mln
    FROM fact_sales_monthly s
    JOIN dim_customer c USING(customer_code)
    JOIN fact_gross_price g USING(product_code)
    WHERE s.fiscal_year = 2021
    GROUP BY channel
)
SELECT *, 
       ROUND((gross_sales_mln / SUM(gross_sales_mln) OVER()) * 100, 2) AS percentage
FROM cte;
```
**Insight**:  
This analysis reveals the contribution of each sales channel, helping businesses understand the effectiveness of different distribution methods. Optimizing high-performing channels and improving low-performing ones can significantly enhance sales performance.

### 10. Top-Selling Products by Division
**Objective**: Identify the top 3 best-selling products in each division for the fiscal year 2021, providing insights into product performance across different divisions.

**Query Techniques**:
- Summing up sold quantities for each product using `SUM()`.
- Ranking products within each division using `ROW_NUMBER()` for identifying the top performers.
- Using `PARTITION BY` within the `ROW_NUMBER()` function to rank products by division.

```sql
WITH cte AS (
    SELECT division, 
           p.product_code, 
           p.product, 
           SUM(sold_quantity) AS total_sold_qty
    FROM fact_sales_monthly s
    JOIN dim_customer c USING(customer_code)
    JOIN dim_product p USING(product_code)
    WHERE s.fiscal_year = 2021
    GROUP BY division, p.product_code, p.product
),
rnk AS (
    SELECT division, 
           product_code, 
           product, 
           total_sold_qty,
           ROW_NUMBER() OVER (PARTITION BY division ORDER BY total_sold_qty DESC) AS rank_order
    FROM cte
)
SELECT division, 
       product_code, 
       product, 
       rank_order, 
       total_sold_qty
FROM rnk
WHERE rank_order <= 3;
```
**Insight**:  
Identifying the top-selling products within each division provides insight into product performance, which can inform strategic decisions about inventory management, product marketing, and sales efforts. By focusing on the highest performers, businesses can leverage these products for maximum profitability.

## Key Learnings

- **SQL Proficiency**: Gained deeper understanding of writing efficient queries using joins, CTEs, filtering, and aggregation techniques.
- **Data Modeling Thinking**: Learned to structure data logically to answer business questions effectively.
- **Analytical Approach**: Improved at breaking down complex requests into sequential SQL logic for accurate analysis.
- **Window Functions**: Mastered ranking and partitioning to derive advanced insights such as top-N analysis.
- **Business Insight Development**: Enhanced ability to translate raw data into meaningful metrics like growth rates, discount trends, and contribution analysis.
- **Clean Query Writing**: Practiced writing readable, maintainable SQL scripts ideal for team collaboration or review.
- **Decision-Oriented Reporting**: Focused on crafting insights useful for executive-level business decision-making.


