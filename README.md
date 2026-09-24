# SQL Relational Database & Advanced Analytics

## Business Problem and Context

TokoKita required a comprehensive relational database analysis to transition from basic spreadsheet reports to enterprise-level data querying. The business needed structured insights regarding customer transaction behaviors, high-value customer rankings, regional revenue distributions, and Month-on-Month (MoM) revenue velocity.

The objective of this project was to design relational table schemas and execute advanced SQL scripts using SQL Server Management Studio (SSMS). By utilizing filtering, conditional aggregations, multi-table joins, Common Table Expressions (CTEs), and Window Functions (`DENSE_RANK` and `LAG`), this project delivers data-driven insights for customer retention and revenue optimization.

## Dataset Overview and Schemas

The database structure consists of three core tables storing customer demographics, order histories, and longitudinal sales records.

* Database Engine: SQL Server Management Studio (SSMS) / T-SQL
* Tables Analyzed: `orders`, `customers`, `transactions`, `sales_data`

### Data Dictionary

* `order_id` / `transaction_id`: Unique identifier for each order record
* `customer_id`: Unique key mapping transactions to registered customer profiles
* `customer_name`: Registered name of the customer
* `category`: Product group classification (Elektronika, Pakaian, Kecantikan)
* `sales_amount` / `amount`: Monetary transaction value in IDR
* `payment_status`: Payment status flag (SUCCESS, FAILED, PENDING)
* `city`: Geographic city location of the customer
* `order_date`: Date timestamp of purchase transaction

## Methodology, Queries, and Result Tables

### 1. Basic Filtering and Sorting

Filtering valid completed orders and sorting high-ticket transactions.

```
-- Query 1: Filter all successful transactions
SELECT *
FROM orders
WHERE payment_status = 'SUCCESS';
```

| Order ID | Customer Name | Category | Sales Amount | Payment Status |
| :--- | :--- | :--- | :--- | :--- |
| TRX-001 | Budi Santoso | Elektronika | Rp 8.500.000 | SUCCESS |
| TRX-002 | Siti Aminah | Pakaian | Rp 450.000 | SUCCESS |
| TRX-003 | Andi Wijaya | Elektronika | Rp 12.000.000 | SUCCESS |
| TRX-005 | Rina Nose | Pakaian | Rp 350.000 | SUCCESS |
| TRX-006 | Eko Prabowo | Elektronika | Rp 5.500.000 | SUCCESS |


```
-- Query 2: Filter specific category columns
SELECT customer_name, category, sales_amount
FROM orders
WHERE category = 'Elektronika';
```

| Customer Name | Category | Sales Amount |
| :--- | :--- | :--- |
| Budi Santoso | Elektronika | Rp 8.500.000 |
| Andi Wijaya | Elektronika | Rp 12.000.000 |
| Eko Prabowo | Elektronika | Rp 5.500.000 |

```
-- Query 3: Sort successful orders by amount descending
SELECT *
FROM orders
WHERE payment_status = 'SUCCESS'
ORDER BY sales_amount DESC;
```

| Order ID | Customer Name | Category | Sales Amount | Payment Status |
| :--- | :--- | :--- | :--- | :--- |
| TRX-003 | Andi Wijaya | Elektronika | Rp 12.000.000 | SUCCESS |
| TRX-001 | Budi Santoso | Elektronika | Rp 8.500.000 | SUCCESS |
| TRX-006 | Eko Prabowo | Elektronika | Rp 5.500.000 | SUCCESS |
| TRX-002 | Siti Aminah | Pakaian | Rp 450.000 | SUCCESS |
| TRX-005 | Rina Nose | Pakaian | Rp 350.000 | SUCCESS |

### 2. Group Aggregations and HAVING Clause
Summarizing sales figures and average ticket sizes across product categories.

```
-- Query 1: Total and average revenue per category
SELECT 
    category, 
    SUM(sales_amount) AS total_revenue,  
    AVG(sales_amount) AS avg_sales
FROM orders
WHERE payment_status = 'SUCCESS'
GROUP BY category;
```

| Category | Total Revenue | Avg Sales |
| :--- | :--- | :--- |
| Elektronika | Rp 26.000.000 | Rp 8.666.666 |
| Pakaian | Rp 800.000 | Rp 400.000 |

```
-- Query 2: Filter categories with revenue exceeding 1,000,000 IDR
SELECT 
    category, 
    SUM(sales_amount) AS total_revenue
FROM orders
WHERE payment_status = 'SUCCESS'
GROUP BY category
HAVING SUM(sales_amount) > 1000000;
```

| Category | Total Revenue |
| :--- | :--- |
| Elektronika | Rp 26.000.000 |

### 3. Multi-Table Relational Joins
Merging transaction logs with customer demographics to track regional performance and profile activity.

```
-- Query 1: INNER JOIN transactions and customer profile data
SELECT 
    t.transaction_id,
    c.customer_name,
    c.city
FROM transactions t
INNER JOIN customers c ON t.customer_id = c.customer_id;
```

| Transaction ID | Customer Name | City |
| :--- | :--- | :--- |
| TRX-001 | Budi Santoso | Jakarta |
| TRX-002 | Siti Aminah | Bandung |
| TRX-003 | Budi Santoso | Jakarta |

```
-- Query 2: LEFT JOIN to identify inactive customer profiles
SELECT 
    c.customer_name,
    t.transaction_id,
    t.amount
FROM customers c
LEFT JOIN transactions t ON t.customer_id = c.customer_id;
```

| Customer Name | Transaction ID | Amount |
| :--- | :--- | :--- |
| Budi Santoso | TRX-001 | Rp 1.500.000 |
| Budi Santoso | TRX-003 | Rp 2.000.000 |
| Siti Aminah | TRX-002 | Rp 500.000 |
| Andi Wijaya | NULL | NULL |
| Dewi Lestari | NULL | NULL |

```
-- Query 3: Regional revenue aggregation
SELECT
    c.city,
    SUM(t.amount) AS total_revenue,
    COUNT(t.transaction_id) AS jumlah_transaksi
FROM transactions t
LEFT JOIN customers c ON t.customer_id = c.customer_id
WHERE payment_status = 'SUCCESS'
GROUP BY c.city;
```

| City | Total Revenue | Jumlah Transaksi |
| :--- | :--- | :--- |
| NULL | Rp 750.000 | 1 |
| Bandung | Rp 500.000 | 1 |
| Jakarta | Rp 3.500.000 | 2 |

4. Advanced CTEs and Window Functions
Ranking top-tier customers and evaluating Month-on-Month (MoM) sales velocity using advanced windowing.

```
-- Query 1: Customer spend ranking using CTE and DENSE_RANK()
WITH customer_spend AS (
    SELECT customer_id, SUM(amount) AS total_spend 
    FROM sales_data
    GROUP BY customer_id
)
SELECT 
    customer_id, 
    total_spend, 
    DENSE_RANK() OVER (ORDER BY total_spend DESC) AS customer_rank
FROM customer_spend;
```

| Customer ID | Total Spend | Customer Rank |
| :--- | :--- | :--- |
| 101 | Rp 22.300.000 | 1 |
| 103 | Rp 18.500.000 | 2 |
| 102 | Rp 10.500.000 | 3 |
| 104 | Rp 250.000 | 4 |

```
-- Query 2: Monthly sales trend tracking using CTE and LAG()
WITH monthly_sales AS (
    SELECT MONTH(order_date) AS bulan, SUM(amount) AS total_sales
    FROM sales_data
    GROUP BY MONTH(order_date)
)
SELECT
    bulan, 
    total_sales,
    LAG(total_sales) OVER (ORDER BY bulan) AS prev_month_sales
FROM monthly_sales;
```

| Bulan | Total Sales | Prev Month Sales |
| :--- | :--- | :--- |
| 1 | Rp 9.250.000 | NULL |
| 2 | Rp 25.600.000 | Rp 9.250.000 |
| 3 | Rp 72.500.000 | Rp 25.600.000 |

## Key Insights
1. Top Customer Contribution: Customer 101 ranks first with total lifetime spend of 22,300,000 IDR, followed by Customer 103 with 18,500,000 IDR. Top two spenders contribute the vast majority of cumulative revenue.
2. Inactive Profile Flagging: Left join analysis successfully flagged registered users (e.g., Andi Wijaya and Dewi Lestari) with zero transaction histories.
3. Revenue Velocity Volatility: Sales expanded substantially in Month 2 (25,600,000 IDR vs 9,250,000 IDR in Month 1) before experiencing a steep contraction in Month 3 (7,250,000 IDR).

## Business Recommendations
1. VIP Customer Loyalty Retention: Implement dedicated VIP engagement perks for Rank 1 and Rank 2 customers to secure recurring high-ticket orders.
2. Targeted Re-Engagement Campaigns: Automated promotional discounts should be routed to customer profiles identified with NULL order histories to incentivize initial purchases.
3. Real-Time Growth Alerting: Integrate LAG() query functions into scheduled database views to instantly alert leadership when Month-on-Month sales drop below operational baselines.
