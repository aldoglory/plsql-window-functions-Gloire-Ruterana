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
![](/images/customer.PNG)

## 2. Create Products Table
```sql
-- Products table
CREATE TABLE products (
    product_id INT PRIMARY KEY,
    product_name VARCHAR2(100) NOT NULL,
    category VARCHAR2(50) NOT NULL
);


```
![](/images/products.PNG)
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
![](/images/transactions.PNG)

### inserting sample data

## Customers
```sql
INSERT INTO customers (customer_id, name, region) VALUES (1001, 'Jean Uwizeye', 'Kigali');
INSERT INTO customers (customer_id, name, region) VALUES (1002, 'Aline Mukamana', 'Huye');
INSERT INTO customers (customer_id, name, region) VALUES (1003, 'Eric Habimana', 'Musanze');
INSERT INTO customers (customer_id, name, region) VALUES (1004, 'Divine Uwera', 'Kigali');
INSERT INTO customers (customer_id, name, region) VALUES (1005, 'Patrick Ndayisaba', 'Huye');


```
![](/images/insertcustomer.PNG)

# Displaying data inserted
```sql

```
![](/images/selectcustomer.PNG)

## Products
```sql
INSERT INTO products (product_id, product_name, category) VALUES (2001, 'Mango Juice', 'Juice');
INSERT INTO products (product_id, product_name, category) VALUES (2002, 'Passion Juice', 'Juice');
INSERT INTO products (product_id, product_name, category) VALUES (2003, 'Mineral Water', 'Water');
INSERT INTO products (product_id, product_name, category) VALUES (2004, 'Energy Drink', 'Soft Drink');
INSERT INTO products (product_id, product_name, category) VALUES (2005, 'Apple Cider', 'Alcoholic');

```
![](/images/insert_product.PNG)
# DispLaying data inserted
```sql

```
![](/images/select_products.PNG)

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
![](/images/insert_transaction.PNG)
# Displaying data inserted
```sql
 
```
![](/images/select_transactions.PNG)


## Window functions

# 1. Ranking: ROW_NUMBER(), RANK(), DENSE_RANK(), PERCENT_RANK()

# ROW_NUMBER()
```sql
-- ROW_NUMBER(): assigns a unique row number to each customer ordered by revenue
SELECT 
    c.name AS customer_name,
    SUM(t.amount) AS total_revenue,
    ROW_NUMBER() OVER (ORDER BY SUM(t.amount) DESC) AS row_num
FROM customers c
JOIN transactions t ON c.customer_id = t.customer_id
GROUP BY c.name
ORDER BY total_revenue DESC;

```
Every customer gets a unique sequential number.

Even if two customers have the same revenue, they won’t share the same number.
![](/images/rownbr().PNG)


# 2. RANK()

```sql
-- RANK(): assigns ranks but leaves gaps if there are ties
SELECT 
    c.name AS customer_name,
    SUM(t.amount) AS total_revenue,
    RANK() OVER (ORDER BY SUM(t.amount) DESC) AS rank_pos
FROM customers c
JOIN transactions t ON c.customer_id = t.customer_id
GROUP BY c.name
ORDER BY total_revenue DESC;

```

![](/images/rank().PNG)
Customers with equal revenue share the same rank.

The next rank skips a number (e.g., 1, 2, 2, 4).

# 3. DENSE_RANK()

```sql
-- DENSE_RANK(): assigns ranks without gaps when there are ties
SELECT 
    c.name AS customer_name,
    SUM(t.amount) AS total_revenue,
    DENSE_RANK() OVER (ORDER BY SUM(t.amount) DESC) AS dense_rank_pos
FROM customers c
JOIN transactions t ON c.customer_id = t.customer_id
GROUP BY c.name
ORDER BY total_revenue DESC;

```

![](/images/denserank().PNG)
Customers with equal revenue share the same rank.

The next rank is continuous (e.g., 1, 2, 2, 3).

# 4. PERCENT_RANK()
```sql
-- PERCENT_RANK(): relative rank between 0 and 1
SELECT 
    c.name AS customer_name,
    SUM(t.amount) AS total_revenue,
    PERCENT_RANK() OVER (ORDER BY SUM(t.amount) DESC) AS percent_rank_pos
FROM customers c
JOIN transactions t ON c.customer_id = t.customer_id
GROUP BY c.name
ORDER BY total_revenue DESC;


```
![](/images/percentagerank().PNG)
Returns a value between 0 and 1.

0 = top customer, 1 = bottom customer.

Useful for percentile-based segmentation.











