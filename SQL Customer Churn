--Relational Database Concepts & Basic SQL Queries
-- Buat Tabel Orders
--CREATE TABLE orders (
--    order_id VARCHAR(10),
--    customer_name VARCHAR(50),
--    category VARCHAR(30),
--    sales_amount INT,
--    payment_status VARCHAR(20)
--);

---- Insert Data Sample
--INSERT INTO orders VALUES
--('TRX-001', 'Budi Santoso', 'Elektronika', 8500000, 'SUCCESS'),
--('TRX-002', 'Siti Aminah', 'Pakaian', 450000, 'SUCCESS'),
--('TRX-003', 'Andi Wijaya', 'Elektronika', 12000000, 'SUCCESS'),
--('TRX-004', 'Dewi Lestari', 'Kecantikan', 250000, 'FAILED'),
--('TRX-005', 'Rina Nose', 'Pakaian', 350000, 'SUCCESS'),
--('TRX-006', 'Eko Prabowo', 'Elektronika', 5500000, 'SUCCESS');


--Perintah Query
-- 1
SELECT *
FROM orders
WHERE payment_status = 'SUCCESS';

-- 2
SELECT customer_name, category, sales_amount
FROM orders
WHERE category = 'Elektronika';

-- 3
SELECT *
FROM orders
WHERE payment_status = 'SUCCESS'
ORDER by sales_amount DESC;


--Data Aggregation & Grouping
-- 1
SELECT category, SUM(sales_amount) AS total_revenue,  AVG(sales_amount) AS avg_sales
FROM orders
WHERE payment_status = 'SUCCESS'
GROUP BY category

-- 2
SELECT category, COUNT(order_id) AS total_orders, SUM(sales_amount) AS total_revenue
FROM orders
WHERE payment_status = 'SUCCESS'
GROUP BY category

-- Soal 3
SELECT category, SUM(sales_amount) AS total_revenue
FROM orders
WHERE payment_status = 'SUCCESS'
GROUP BY category
HAVING SUM(sales_amount) > 1000000



----Multi-Table Analysis with Joins
---- 1. Buat Tabel Customers
--CREATE TABLE customers (
--    customer_id INT PRIMARY KEY,
--    customer_name VARCHAR(50),
--    city VARCHAR(50)
--);

---- 2. Buat Tabel Orders Baru
--CREATE TABLE transactions (
--    transaction_id VARCHAR(10),
--    customer_id INT,
--    amount INT,
--    payment_status VARCHAR(20)
--);

---- 3. Insert Data Sample
--INSERT INTO customers VALUES
--(101, 'Budi Santoso', 'Jakarta'),
--(102, 'Siti Aminah', 'Bandung'),
--(103, 'Andi Wijaya', 'Surabaya'),
--(104, 'Dewi Lestari', 'Medan'); -- Dewi belum pernah belanja

--INSERT INTO transactions VALUES
--('TRX-001', 101, 1500000, 'SUCCESS'),
--('TRX-002', 102, 500000, 'SUCCESS'),
--('TRX-003', 101, 2000000, 'SUCCESS'),
--('TRX-004', 999, 750000, 'SUCCESS'); -- Customer_id 999 tidak ada di tabel customers

-- Soal 1
SELECT 
    t.transaction_id,
    c.customer_name,
    c.city
FROM transactions t
INNER JOIN customers c ON t.customer_id = c.customer_id;

-- Soal 2
SELECT 
    c.customer_name,
    t.transaction_id,
    t.amount
FROM customers c
LEFT JOIN transactions t ON t.customer_id = c.customer_id;

-- Soal 3
SELECT
    c.city,
    SUM(t.amount) AS total_revenue,
    COUNT(t.transaction_id) AS jumlah_transaksi
FROM transactions t
LEFT JOIN customers c ON t.customer_id = c.customer_id
WHERE payment_status = 'SUCCESS'
GROUP BY c.city;

    

----Advanced SQL: Subqueries, CTE (WITH), Window Functions
--CREATE TABLE sales_data (
--    order_id VARCHAR(10),
--    customer_id INT,
--    order_date DATE,
--    category VARCHAR(30),
--    amount INT
--);

--INSERT INTO sales_data VALUES
--('TRX-201', 101, '2026-01-05', 'Elektronika', 8500000),
--('TRX-202', 102, '2026-01-06', 'Pakaian', 450000),
--('TRX-203', 101, '2026-01-15', 'Kecantikan', 300000),
--('TRX-204', 103, '2026-02-01', 'Elektronika', 12000000),
--('TRX-205', 102, '2026-02-10', 'Pakaian', 600000),
--('TRX-206', 101, '2026-02-15', 'Elektronika', 13000000),
--('TRX-207', 104, '2026-03-01', 'Kecantikan', 250000),
--('TRX-208', 103, '2026-03-10', 'Elektronika', 6500000),
--('TRX-209', 101, '2026-03-20', 'Pakaian', 500000);

--Customer Ranking dengan CTE & DENSE_RANK
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

--Monthly Sales Trend dengan CTE & LAG
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

