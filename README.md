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


