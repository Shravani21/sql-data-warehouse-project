# 🏗️ SQL Data Warehouse from Scratch

A full end-to-end Data Engineering project that builds a production-style SQL Data Warehouse using **SQL Server**, implementing a **Bronze → Silver → Gold** medallion architecture with data sourced from CRM and ERP systems.

---

## 📌 Project Overview

This project demonstrates how real-world data warehouses are built — from raw CSV ingestion all the way to a business-ready Star Schema that can power analytics and dashboards.

| Layer | Description | Tables |
|-------|-------------|--------|
| 🥉 Bronze | Raw data loaded as-is from source | 6 tables |
| 🥈 Silver | Cleaned, standardised, deduplicated | 6 tables |
| 🥇 Gold | Business-ready views (Star Schema) | 3 views |

---

## 🗂️ Data Sources

- **CRM System** — Customer, product, and sales transaction data (3 CSV files)
- **ERP System** — Customer demographics, location, and product category data (3 CSV files)

---

## 🔄 Data Flow Diagram

![Data Flow Diagram](data_flow.png)

Data flows from two source systems (CRM and ERP) through three transformation layers:

- **Bronze Layer** — Raw data loaded via `BULK INSERT`. No transformations applied.
- **Silver Layer** — Data is cleaned: NULLs removed, duplicates handled via `ROW_NUMBER()`, dates validated, gender and marital status standardised using `CASE` statements.
- **Gold Layer** — CRM and ERP data joined together into a Star Schema using `LEFT JOIN` across silver tables.

---

## ⭐ Data Model — Star Schema

![Star Schema](star_schema.png)

The Gold layer follows a **Star Schema** design:

- **`gold.fact_sales`** — Central fact table containing all sales transactions (60,398 rows)
- **`gold.dim_customers`** — Customer dimension with demographic data (18,484 rows)
- **`gold.dim_products`** — Product dimension with category and pricing info

**Business Rule:** `Sales Amount = Quantity × Price`

---

## 🛠️ Tech Stack

- **SQL Server Express** — Database engine
- **SSMS** (SQL Server Management Studio) — Query and development environment
- **T-SQL** — All transformations, stored procedures, and views

---

## 📁 Project Structure

```
sql-data-warehouse-project/
│
├── datasets/
│   ├── source_crm/
│   │   ├── cust_info.csv
│   │   ├── prd_info.csv
│   │   └── sales_details.csv
│   └── source_erp/
│       ├── cust_az12.csv
│       ├── loc_a101.csv
│       └── px_cat_g1v2.csv
│
├── scripts/
│   ├── bronze/
│   │   ├── create_bronze_tables.sql
│   │   └── load_bronze.sql
│   ├── silver/
│   │   ├── create_silver_tables.sql
│   │   └── load_silver.sql
│   └── gold/
│       ├── dim_customers.sql
│       ├── dim_products.sql
│       └── fact_sales.sql
│
├── star_schema.png
├── data_flow.png
└── README.md
```

---

## ⚙️ How to Run

1. **Clone the repository**
   ```bash
   git clone https://github.com/yourusername/sql-data-warehouse-project.git
   ```

2. **Create the database in SSMS**
   ```sql
   CREATE DATABASE DataWarehouse;
   ```

3. **Create schemas**
   ```sql
   CREATE SCHEMA bronze;
   CREATE SCHEMA silver;
   CREATE SCHEMA gold;
   ```

4. **Run scripts in order**
   ```
   1. scripts/bronze/create_bronze_tables.sql
   2. scripts/bronze/load_bronze.sql        → EXEC bronze.load_bronze
   3. scripts/silver/create_silver_tables.sql
   4. scripts/silver/load_silver.sql        → EXEC silver.load_silver
   5. scripts/gold/dim_customers.sql
   6. scripts/gold/dim_products.sql
   7. scripts/gold/fact_sales.sql
   ```

---

## 🔍 Key SQL Concepts Used

- `BULK INSERT` with `TABLOCK` for fast CSV loading
- `ROW_NUMBER() OVER (PARTITION BY ... ORDER BY ...)` for deduplication
- `CASE WHEN` for data standardisation (gender, marital status, dates)
- `COALESCE` for NULL handling across joined tables
- `LEFT JOIN` across Silver layer tables to build Gold views
- `CREATE OR ALTER VIEW` for idempotent Gold layer scripts
- Stored procedures with `TRY...CATCH` and execution time logging

---

## ✅ Data Quality Checks

- Duplicate customer records identified and resolved using `ROW_NUMBER()`
- NULL `cst_id` records filtered out in Silver layer
- Invalid dates (value = 0 or wrong length) converted to `NULL`
- Sales amount validated: recalculated as `Quantity × ABS(Price)` when incorrect
- Foreign key integrity verified: 0 orphan records in `fact_sales`

---

## 📊 Row Counts (Final)

| Table | Rows |
|-------|------|
| gold.dim_customers | 18,484 |
| gold.fact_sales | 60,398 |
| gold.dim_products | — |

---

## 👤 Author

**Shrav**
Fresher — Computer Science
Built as a portfolio project to demonstrate Data Engineering and SQL skills.

---

## 🙏 Credits

Project inspired by the **"SQL Data Warehouse from Scratch"** tutorial by [Data with Baraa](https://www.youtube.com/@DataWithBaraa) on YouTube.
