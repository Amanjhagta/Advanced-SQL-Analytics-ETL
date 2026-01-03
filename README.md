# 🛒 Retail Sales & Finance Analytics Project

### 📄 Overview
This project involves analyzing a large dataset of sales records for a hardware retail company (AtliQ Hardware) to generate insights into sales trends, market performance, and supply chain efficiency. 

The project migrates static CSV data into a relational database (MySQL) using a **Star Schema** architecture. It utilizes advanced SQL techniques—including Stored Procedures, Triggers, Events, and Window Functions—to solve business problems such as calculating fiscal year metrics, determining market badges (Gold/Silver), and tracking forecast accuracy.

---

### 🛠️ Technical Stack
* **Database:** MySQL
* **Language:** SQL (Advanced)
* **Data Model:** Star Schema (Facts & Dimensions)
* **Domain:** Consumer Electronics / Retail Analytics

---

### 📂 Database Architecture (Star Schema)
The data model is designed for high-performance read operations using a standard Star Schema.

**1. Dimension Tables (Static Data)**
* `dim_customer`: Customer details (Market, Region, Customer Code).
* `dim_product`: Product hierarchy (Division, Segment, Category, Product Code).
* `dim_date`: Calendar date mapping to Fiscal Years (Sept-Aug).

**2. Fact Tables (Transactional Data)**
* `fact_sales_monthly`: Core sales transactions (Quantity sold).
* `fact_gross_price`: Product base prices per fiscal year.
* `fact_pre_invoice_deductions`: Monthly pre-invoice discount percentages.
* `fact_post_invoice_deductions`: Post-invoice discounts and other deductions.
* `fact_manufacturing_cost`: Production costs per unit.
* `fact_freight_cost`: Shipping and logistics costs.

---

### 🔑 Key SQL Features Implemented

#### 1. User-Defined Functions (UDFs)
Created custom functions to handle the fiscal year mismatch (Financial year starts in September).
* `get_fiscal_year(date)`: Converts standard calendar dates to financial years automatically.

#### 2. Stored Procedures (Automation)
Developed reusable procedures to automate report generation for different markets and customers.
* `get_monthly_gross_sales_for_customer`: Generates monthly sales reports dynamically based on customer codes.
* `get_market_badge`: Logic to assign 'Gold' or 'Silver' status to markets based on total sold quantity (>5M units).
* `get_top_n_markets_by_net_sales`: Returns the top performing markets for any given fiscal year.

#### 3. Database Views (Virtual Tables)
Simplifying complex query chains for financial reporting.
* `sales_preinv_discount`: Joins sales data with pre-invoice deductions.
* `net_sales`: The final reporting view that calculates Net Sales after applying all post-invoice discounts.

#### 4. Window Functions
Used for advanced analytics without collapsing rows.
* `OVER(PARTITION BY ...)`: Calculated "Share of Wallet" (Percentage of total sales per region).
* `DENSE_RANK()`: Identified top 3 products in every division simultaneously.

#### 5. Triggers & Events (Data Engineering)
* **Triggers:** Automatically updates a helper table `fact_act_est` (Actual vs Estimated) whenever new sales or forecast data is inserted.
* **Events:** A scheduled job (`e_daily_log_purge`) that automatically deletes session logs older than 5 days to maintain database hygiene.

---

### 📊 Business Reports Generated

**1. Gross Sales Report:**
* Monthly product-level sales aggregated by customer.
* Logic: `Sold Quantity` * `Gross Price`.

**2. Net Sales & P&L Analysis:**
* Calculates real revenue after all deductions.
* Logic: `Gross Sales` - `Pre-Invoice Discounts` - `Post-Invoice Deductions`.

**3. Supply Chain Forecast Accuracy:**
* Comparison of `Forecast Quantity` vs `Actual Sold Quantity`.
* Derived an accuracy percentage to measure supply chain reliability using a helper table.

---

### 🚀 Performance Optimization
* **Index Utilization:** Replaced the `get_fiscal_year` function in `WHERE` clauses with a Join on `dim_date` to allow the database to use indexes, significantly reducing query execution time for large datasets (1.5M+ rows).
* **Denormalization:** Added `fiscal_year` directly to the `fact_sales_monthly` table to minimize expensive joins during time-series analysis.

---

### 📝 Usage
1.  **Import Data:** Load the CSV files into MySQL.
2.  **Schema Setup:** Run the schema creation scripts to define tables and relationships.
3.  **Functions & Procedures:** Execute the definition scripts to register UDFs and Stored Procs.
4.  **Reporting:** Call the stored procedures to generate specific reports:
    ```sql
    CALL get_top_n_markets_by_net_sales(2021, 5);
    ```

---

### 👤 Author
* **Aman Jhagta** 
* *Concept based on AtliQ Hardware Case Study*
