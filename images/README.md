# PL/SQL Window Functions -- Kigali Beverage Distributors Ltd.

## 📌 Business Problem

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

## ✅ Success Criteria (Goals)

This project demonstrates 5 measurable analytics goals:

1.  **Top 5 products per region/quarter** → `RANK()`\
2.  **Running monthly sales totals** → `SUM() OVER()`\
3.  **Month-over-month growth analysis** → `LAG()` / `LEAD()`\
4.  **Customer quartiles (segmentation)** → `NTILE(4)`\
5.  **3-month moving average of sales** → `AVG() OVER()`

------------------------------------------------------------------------

## 🗄️ Database Schema

### Tables

-   **customers** (customer_id, name, region)\
-   **products** (product_id, name, category)\
-   **transactions** (transaction_id, customer_id, product_id,
    sale_date, amount)

### ER Diagram

*(Insert diagram image here)*

------------------------------------------------------------------------

## 🖥️ Window Function Queries

Each function is demonstrated with:\
- SQL query (with comments)\
- Screenshot of results\
- 2--3 sentence interpretation

### Examples:

-   **Ranking Functions:** Identify top N customers by revenue.\
-   **Aggregate Functions:** Running totals and trends.\
-   **Navigation Functions:** Compare current vs. previous months.\
-   **Distribution Functions:** Segment customers into quartiles.

------------------------------------------------------------------------

## 📊 Results Analysis

### Descriptive -- What happened?

-   Identified top-selling products in Kigali region.\
-   Sales peaked in Q2 but slowed in Q3.

### Diagnostic -- Why?

-   Product promotions influenced Q2 spike.\
-   Q3 decline due to transportation delays.

### Prescriptive -- What next?

-   Expand distribution of high-demand products.\
-   Introduce loyalty programs for low-frequency customers.

------------------------------------------------------------------------

## 📂 Repository Structure

    plsql-window-functions-[lastname]-[firstname]/
    │── sql/               # SQL scripts
    │── screenshots/        # Query outputs
    │── er_diagram.png      # Schema visualization
    │── README.md           # Documentation

------------------------------------------------------------------------

## 📚 References

*(At least 10 sources, e.g. Oracle docs, tutorials, academic articles,
business analytics resources)*

"All sources were properly cited. Implementations and analysis represent
original work. No AI-generated content was copied without attribution or
adaptation."

------------------------------------------------------------------------

✍️ Integrity Note:\
As database professionals, we uphold accuracy, confidentiality, and
integrity.
