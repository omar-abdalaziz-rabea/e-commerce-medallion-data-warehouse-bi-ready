🛒 Olist E-Commerce | End-to-End Data Datawarehouse, ETL & Snowflake Modeling Using Python

📌 Project Overview

An end-to-end data preparation and modeling project built with Python, Pandas, and PyArrow, focused on transforming raw e-commerce datasets into clean, optimized, validated, and analysis-ready data.

The project implements a structured data engineering workflow covering data profiling, data quality assessment, data type optimization, transformation, normalization, surrogate key generation, Snowflake Schema design, and referential integrity validation.

The final output is a structured analytical data model designed to minimize data redundancy, reduce memory consumption, and provide a reliable foundation for downstream analytics and business intelligence.

---

🚀 Key Engineering Achievements

1️⃣ Memory Optimization Through Data Type Selection

Optimized memory consumption by replacing inefficient Pandas "object" data types with more appropriate types based on column characteristics.

Metric| Memory Usage
Before Optimization| 121,491,838 Bytes
After Optimization| 56,322,496 Bytes
Memory Saved| 65,169,342 Bytes
Memory Reduction| 53.6%

Approximately 63,642 KiB saved through appropriate data type selection alone.

Key optimizations:

- "string[pyarrow]" for identifier columns.
- "category" for low-cardinality categorical columns.
- "datetime64[s]" and "date32[pyarrow]" for date columns.
- Nullable integer and floating-point types for numerical attributes.

2️⃣ Memory Optimization Through Data Normalization

Reduced memory consumption by restructuring datasets and eliminating redundant attributes through normalization and relational modeling.

Metric| Memory Usage
Before Normalization| 99,742,060 Bytes
After Normalization| 34,865,124 Bytes
Memory Saved| 64,876,936 Bytes
Memory Reduction| 65.1%

Approximately 63,356 KiB saved through data normalization and redundancy reduction.

Key optimizations:

- Extracted shared geographical attributes into a dedicated "dim_location" table.
- Replaced repeated geographical attributes with "location_key" references.
- Established customer relationships through "customer_key".
- Introduced surrogate keys to establish relationships between fact and dimension tables.
- Reduced redundant storage of descriptive attributes.

These are two independent optimization stages, demonstrating the impact of both data type selection and relational data modeling on memory efficiency.

---

🏗️ Project Architecture

The project consists of three major engineering stages:

1. Data Profiling & Cleaning
2. Data Transformation & Snowflake Modeling
3. Data Integrity & Relationship Validation

🔄 Data Flow

             Raw Data
                 |
                 v
         Data Profiling
                 |
                 v
       Data Cleaning & Transformation
                 |
                 v
       Data Normalization
                 |
                 v
      Surrogate Key Generation
                 |
                 v
         Snowflake Model
                 |
                 v
       Integrity Validation
                 |
                 v
          Modeled Data
                 |
                 v
        Analysis-Ready Data

---

🧹 1. Data Profiling & Transformation

The first stage focuses on understanding the structure, quality, and characteristics of each source table before applying transformations.

🔍 Data Profiling

- Inspected the structure and dimensions of each dataset.
- Defined the grain of each table by identifying what each row represents.
- Generated exploratory data profiling reports using "data-profiling".
- Examined column distributions, data types, missing values, and potential quality issues.

⚙️ Transformation Workflow

The following procedures were implemented:

1. Column Name Standardization: Standardized column naming conventions using "snake_case" where required.
2. Data Type Optimization: Selected appropriate data types to reduce memory consumption and improve processing efficiency.
3. Missing Value Assessment: Identified and evaluated missing values across all datasets.
4. Duplicate Detection: Identified and removed duplicate records.
5. Text Standardization: Validated identifier formats and standardized geographical text attributes.
6. Numerical Validation: Checked numerical ranges and business constraints.
7. Date Standardization: Converted date columns into appropriate datetime and date representations.
8. Business Rule Application: Handled missing numerical product attributes and corrected invalid chronological sequences in order delivery dates.
9. Column Organization: Reordered columns according to logical data categories.
10. Data Integrity Checks: Applied validation rules before persisting transformed datasets.

📊 Data Quality Assessment by Table

Dataset| Initial Rows| Quality Issues / Actions
Orders| 112,372| 14,455 duplicate rows; missing timestamps; chronological inconsistencies
Customers| 99,441| No duplicate rows or missing values; identifier validation
Order Items| 156,424| 37,171 duplicate rows; numerical and identifier validation
Order Reviews| 97,455| Review score and timestamp validation
Products| 32,951| Missing numerical attributes handled using zero
Sellers| 3,095| Identifier and geographical attribute validation

💾 Data Persistence

Transformed datasets were stored in Parquet format using PyArrow.

- Preserved optimized data types.
- Used efficient columnar storage.
- Generated HTML profiling reports for individual source datasets.

---

🏛️ 2. Data Normalization & Snowflake Modeling

After completing the cleaning and transformation stage, the datasets were restructured into a relational data model.

The primary objective was to reduce data redundancy, establish consistent relationships, and create an optimized analytical structure.

🔑 Surrogate Key Generation

Generated surrogate keys for the main dimension entities:

- "order_key"
- "customer_key"
- "product_key"
- "seller_key"
- "location_key"

These keys provide internal identifiers for modeled entities and establish relationships between fact and dimension tables.

🔄 Normalization Strategy

📍 Location Normalization

Customer and seller geographical attributes were initially stored in their respective datasets.

To eliminate repeated city and state combinations:

1. Extracted customer and seller geographical attributes.
2. Combined both datasets into a unified location dataset.
3. Removed duplicate geographical combinations.
4. Generated a unique "location_key".
5. Replaced the original geographical attributes with "location_key" references in customer and seller dimensions.

This resulted in a shared "dim_location" dimension.

👥 Customer Relationship Optimization

Customer relationships were moved to the order dimension through "customer_key".

The original "customer_id" reference was removed from the customer dimension after establishing the surrogate-key relationship.

The customer business identifier was retained in the customer dimension under the name "customer_id", based on the source "customer_unique_id".

This separates customer identification from order-level relationships and reduces redundant customer references.

❄️ Final Snowflake Schema

The resulting model consists of the following tables:

📦 Fact Table

"fact_sales"

Grain: One row per order item.

Contains:

- "order_key"
- "product_key"
- "seller_key"
- "order_item_id"
- "price"
- "freight_value"
- "shipping_limit_date"

📚 Dimension Tables

Dimension| Description
"dim_orders"| Order attributes, customer relationship, order status, timestamps, and review information
"dim_customers"| Customer identifiers and location relationship
"dim_products"| Product descriptive attributes
"dim_sellers"| Seller identifiers and location relationship
"dim_location"| Shared city and state attributes

🔗 Model Relationships

- "fact_sales" -> "dim_orders"
- "fact_sales" -> "dim_products"
- "fact_sales" -> "dim_sellers"
- "dim_orders" -> "dim_customers"
- "dim_customers" -> "dim_location"
- "dim_sellers" -> "dim_location"

The resulting structure follows a Snowflake Schema, where selected dimensions are further normalized into related dimensions to minimize redundancy.

---

✅ 3. Data Integrity & Relationship Validation

The final stage ensures that the modeled datasets maintain valid relationships, primary key uniqueness, and referential integrity.

Validation was implemented using Python assertions and Pandas operations.

🔑 Primary Key Validation

Verified uniqueness of the following dimension business identifiers:

- "dim_orders.order_id"
- "dim_products.product_id"
- "dim_sellers.seller_id"

Additionally, checked uniqueness of:

- "dim_customers.customer_key"
- "dim_location.location_key"

🔗 Foreign Key Validation

Verified that foreign key values reference existing records in their corresponding dimension tables.

Foreign Key| Referenced Key
"fact_sales.order_key"| "dim_orders.order_key"
"fact_sales.product_key"| "dim_products.product_key"
"fact_sales.seller_key"| "dim_sellers.seller_key"
"dim_orders.customer_key"| "dim_customers.customer_key"
"dim_customers.location_key"| "dim_location.location_key"
"dim_sellers.location_key"| "dim_location.location_key"

These checks ensure that modeled relationships do not contain orphaned foreign key references.

All implemented assertions passed successfully.

---

🛠️ Technology Stack

Technology| Purpose
Python| Data processing and transformation
Pandas| Data manipulation, cleaning, and modeling
NumPy| Numerical operations and conditional transformations
PyArrow| Optimized data types and Parquet storage
data-profiling| Exploratory data profiling and reporting
Parquet| Efficient columnar data storage

---

🎯 Final Outcome

The project transforms raw e-commerce datasets into a structured, optimized, and validated analytical data model.

Key Deliverables

- 📊 Profiled and documented source datasets.
- 🧹 Cleaned and standardized datasets.
- 🚀 53.6% memory reduction through data type optimization.
- 🔄 65.1% memory reduction through data normalization.
- 🔑 Surrogate key-based relationships.
- ❄️ A Snowflake Schema consisting of fact and dimension tables.
- ✅ Primary key uniqueness and foreign key referential integrity validation.
- 💾 Optimized Parquet datasets ready for downstream analytical workloads.

The final modeled datasets provide a clean, structured, and validated foundation for exploratory data analysis, statistical analysis, business intelligence, and reporting.

---

![Model Diagram](Snowflake_Schema_Model.png)

---

Project Focus: Data Cleaning | Data Transformation | Memory Optimization | Data Normalization | Surrogate Keys | Snowflake Schema | Data Quality | Referential Integrity | Python Data Engineering
