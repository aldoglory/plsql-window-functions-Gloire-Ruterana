# PL/SQL Window Functions -- Kigali Beverage Distributors Ltd.

## Business Problem

Kigali Beverage Distributors Ltd., a regional beverage transportation
and distribution company, needs advanced analytics to support its
**Sales & Marketing Department**.\
The current challenge is to analyze customer behavior, product
performance, and sales trends efficiently. Managers want insights that
go beyond totals and averages --- they need ranking, segmentation, and
growth tracking to make data-driven decisions.

**Expected Outcome:**\
Generate actionable insights on top-performing products, customer
segmentation, and sales trends using **PL/SQL window functions**.

------------------------------------------------------------------------

##  Success Criteria (Goals)

This project demonstrates 5 measurable analytics goals:

1.  **Top 5 products per region/quarter** → `RANK()`\
2.  **Running monthly sales totals** → `SUM() OVER()`\
3.  **Month-over-month growth analysis** → `LAG()` / `LEAD()`\
4.  **Customer quartiles (segmentation)** → `NTILE(4)`\
5.  **3-month moving average of sales** → `AVG() OVER()`

------------------------------------------------------------------------

## Database Schema

### Tables

-   **customers** (customer_id, name, region)\
-   **products** (product_id, name, category)\
-   **transactions** (transaction_id, customer_id, product_id,
    sale_date, amount)

### ER Diagram

![](/images/ER_Diagram_Beverage_Distributor.png)

### creating tables 
## 1. Create Customers Table
```sql
-- Customers table
CREATE TABLE customers (
    customer_id INT PRIMARY KEY,
    name VARCHAR2(100) NOT NULL,
    region VARCHAR2(50) NOT NULL
);

```
![](/images/customer.png)

## 2. Create Products Table
```sql
-- Products table
CREATE TABLE products (
    product_id INT PRIMARY KEY,
    product_name VARCHAR2(100) NOT NULL,
    category VARCHAR2(50) NOT NULL
);


```
![](/images/products.png)
## 3. Create Transactions Table
```sql
-- Transactions table
CREATE TABLE transactions (
    transaction_id INT PRIMARY KEY,
    customer_id INT,
    product_id INT,
    sale_date DATE NOT NULL,
    amount NUMBER(12,2) NOT NULL,
    CONSTRAINT fk_customer FOREIGN KEY (customer_id) REFERENCES customers(customer_id),
    CONSTRAINT fk_product FOREIGN KEY (product_id) REFERENCES products(product_id)
);


```
![](/images/transactions.png)

### inserting sample data

## Customers
```sql
INSERT INTO customers (customer_id, name, region) VALUES (1001, 'Jean Uwizeye', 'Kigali');
INSERT INTO customers (customer_id, name, region) VALUES (1002, 'Aline Mukamana', 'Huye');
INSERT INTO customers (customer_id, name, region) VALUES (1003, 'Eric Habimana', 'Musanze');
INSERT INTO customers (customer_id, name, region) VALUES (1004, 'Divine Uwera', 'Kigali');
INSERT INTO customers (customer_id, name, region) VALUES (1005, 'Patrick Ndayisaba', 'Huye');


```

## Products
```sql
INSERT INTO products (product_id, product_name, category) VALUES (2001, 'Mango Juice', 'Juice');
INSERT INTO products (product_id, product_name, category) VALUES (2002, 'Passion Juice', 'Juice');
INSERT INTO products (product_id, product_name, category) VALUES (2003, 'Mineral Water', 'Water');
INSERT INTO products (product_id, product_name, category) VALUES (2004, 'Energy Drink', 'Soft Drink');
INSERT INTO products (product_id, product_name, category) VALUES (2005, 'Apple Cider', 'Alcoholic');

```
## Transactions

```sql
INSERT INTO transactions (transaction_id, customer_id, product_id, sale_date, amount) 
VALUES (3001, 1001, 2001, DATE '2025-01-15', 25000);

INSERT INTO transactions (transaction_id, customer_id, product_id, sale_date, amount) 
VALUES (3002, 1002, 2003, DATE '2025-01-20', 15000);

INSERT INTO transactions (transaction_id, customer_id, product_id, sale_date, amount) 
VALUES (3003, 1003, 2004, DATE '2025-02-05', 30000);

INSERT INTO transactions (transaction_id, customer_id, product_id, sale_date, amount) 
VALUES (3004, 1001, 2002, DATE '2025-02-18', 18000);

INSERT INTO transactions (transaction_id, customer_id, product_id, sale_date, amount) 
VALUES (3005, 1004, 2005, DATE '2025-03-10', 22000);

INSERT INTO transactions (transaction_id, customer_id, product_id, sale_date, amount) 
VALUES (3006, 1005, 2001, DATE '2025-03-22', 27000);

INSERT INTO transactions (transaction_id, customer_id, product_id, sale_date, amount) 
VALUES (3007, 1002, 2002, DATE '2025-04-02', 21000);

INSERT INTO transactions (transaction_id, customer_id, product_id, sale_date, amount) 
VALUES (3008, 1003, 2003, DATE '2025-04-15', 12000);

INSERT INTO transactions (transaction_id, customer_id, product_id, sale_date, amount) 
VALUES (3009, 1004, 2004, DATE '2025-05-05', 28000);

INSERT INTO transactions (transaction_id, customer_id, product_id, sale_date, amount) 
VALUES (3010, 1005, 2005, DATE '2025-05-20', 32000);



```

## Window functions

# 1. Ranking: ROW_NUMBER(), RANK(), DENSE_RANK(), PERCENT_RANK()
```sql
-- Ranking functions: Top N customers by revenue
SELECT 
    c.name AS customer_name,
    SUM(t.amount) AS total_revenue,

    -- Assigns a unique row number (no ties)
    ROW_NUMBER() OVER (ORDER BY SUM(t.amount) DESC) AS row_num,

    -- Standard ranking (ties leave gaps, e.g. 1, 2, 2, 4)
    RANK() OVER (ORDER BY SUM(t.amount) DESC) AS rank_pos,

    -- Dense rank (ties don't leave gaps, e.g. 1, 2, 2, 3)
    DENSE_RANK() OVER (ORDER BY SUM(t.amount) DESC) AS dense_rank_pos,

    -- Percent rank (relative position between 0 and 1)
    PERCENT_RANK() OVER (ORDER BY SUM(t.amount) DESC) AS percent_rank_pos

FROM transactions t
JOIN customers c ON t.customer_id = c.customer_id
GROUP BY c.name
ORDER BY total_revenue DESC;


```

# 2. Aggregate: SUM(), AVG(), MIN()
```sql
-- Aggregate window functions: Running totals & trends
SELECT 
    t.transaction_id,
    t.sale_date,
    t.amount,

    -- Running total of sales (ROWS: physical row count)
    SUM(t.amount) OVER (
        ORDER BY t.sale_date 
        ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
    ) AS running_total_rows,

    -- Running average (RANGE: considers values within a logical range of dates)
    AVG(t.amount) OVER (
        ORDER BY t.sale_date 
        ROWS BETWEEN 2 PRECEDING AND CURRENT ROW
    ) AS moving_avg_rows,

    -- Running minimum
    MIN(t.amount) OVER (
        ORDER BY t.sale_date 
        ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
    ) AS running_min,

    -- Running maximum
    MAX(t.amount) OVER (
        ORDER BY t.sale_date 
        ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
    ) AS running_max

FROM transactions t
ORDER BY t.sale_date;



```
# 3. Navigation: LAG(), LEAD()
```sql
-- Navigation window functions: Month-to-month analysis
WITH monthly_sales AS (
    SELECT 
        FORMAT(t.sale_date, 'yyyy-MM') AS month,
        SUM(t.amount) AS total_sales
    FROM transactions t
    GROUP BY FORMAT(t.sale_date, 'yyyy-MM')
)
SELECT 
    month,
    total_sales,

    -- Previous month's sales
    LAG(total_sales, 1) OVER (ORDER BY month) AS prev_month_sales,

    -- Next month's sales
    LEAD(total_sales, 1) OVER (ORDER BY month) AS next_month_sales,

    -- Growth % compared to previous month
    ROUND(
        CASE 
            WHEN LAG(total_sales, 1) OVER (ORDER BY month) = 0 THEN NULL
            ELSE ( (total_sales - LAG(total_sales, 1) OVER (ORDER BY month)) * 100.0 / 
                    LAG(total_sales, 1) OVER (ORDER BY month) )
        END, 2
    ) AS growth_percent
FROM monthly_sales
ORDER BY month;


```
# 4. Distribution: NTILE(4), CUME_DIST() 
```sql
-- Distribution window functions: Customer segmentation
SELECT 
    c.name AS customer_name,
    SUM(t.amount) AS total_spent,

    -- Divide customers into 4 quartiles (Q1 = top spenders)
    NTILE(4) OVER (ORDER BY SUM(t.amount) DESC) AS spending_quartile,

    -- Cumulative distribution (between 0 and 1)
    CUME_DIST() OVER (ORDER BY SUM(t.amount) DESC) AS spending_cume_dist

FROM customers c
JOIN transactions t ON c.customer_id = t.customer_id
GROUP BY c.name
ORDER BY total_spent DESC;


```








