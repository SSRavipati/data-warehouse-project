# SQL Data Warehouse from Scratch

**By \Sankari — Data Engineer**

An end-to-end **ETL** pipeline built on Microsoft SQL Server, showcasing data ingestion, transformation, and dimensional modeling using the **Bronze–Silver–Gold** medallion architecture.

---

## Table of Contents

1. [Project Overview](#project-overview)
2. [Features](#features)
3. [Prerequisites](#prerequisites)
4. [Installation & Setup](#installation--setup)
5. [Usage](#usage)
6. [Project Structure](#project-structure)
7. [Running the Pipeline](#running-the-pipeline)
8. [Testing & Validation](#testing--validation)
9. [Contributing](#contributing)

---

## Project Overview

This repository demonstrates a production-style **data warehouse** built from raw CSV sources into a star-schema model. It follows the **Medallion Architecture**:

* **Bronze (Staging)**: Raw ingestion using `BULK INSERT` into staging tables.
* **Silver (Cleansed)**: Data cleansing, de-duplication, normalization, and type conversions.
* **Gold (Analytics)**: Dimensional modeling with facts and dimensions optimized for reporting.

**Technologies & Versions:**

* Microsoft SQL Server 2019 (Developer Edition)
* SQL Server Management Studio 18.10
* Windows 10 / Linux environment

**Key Learning Outcomes:**

* Designing and automating layered ETL processes
* Implementing incremental loads and parallelism for performance
* Applying robust error handling and audit logging
* Building production-ready star schemas for business insights

---

## Features

* **Automated Bulk Loads**: Bronze layer ingestion with `BULK INSERT` and table truncation.
* **Advanced Transformations**: Silver layer uses window functions, lookup tables, and date mapping.
* **Incremental Loads & Parallelism**: Demonstrated patterns for processing only new or changed data and parallel job steps.
* **Comprehensive Logging**: Load metadata tables capture start/end timestamps, row counts, and durations for observability.
* **Dimensional Modeling**: Gold layer creates conformed dimensions and facts in a star schema.
* **Error Handling**: Stored procedures wrap ETL steps in `TRY/CATCH`, logging errors for easy debugging.

---

## Prerequisites

* **Microsoft SQL Server 2019** or later
* **SQL Server Management Studio (SSMS) 18.10**
* Windows or Linux host with filesystem access to sample data
* **Git** for cloning the repository

---

## Installation & Setup

1. **Clone the repository**

   ```bash
   git clone https://github.com/YourUser/sql-data-warehouse-project.git
   cd sql-data-warehouse-project
   ```
2. **Create the database**

   ```sql
   CREATE DATABASE DataWarehouse;
   GO
   ```
3. **Create schemas**

   ```sql
   USE DataWarehouse;
   GO
   CREATE SCHEMA bronze;  -- staging/raw
   CREATE SCHEMA silver;  -- cleansed/transformed
   CREATE SCHEMA gold;    -- dimensional models
   ```
4. **Place sample CSV files**

   * Copy `.csv` files from `datasets/` to your local path (e.g., `C:\dw\data\bronze\`)
   * Update file paths in `scripts/bronze_load.sql` if necessary.

---

## Usage

1. **Load Bronze layer**

   ```sql
   USE DataWarehouse;
   EXEC bronze.load_bronze;
   ```
2. **Load Silver layer**

   ```sql
   USE DataWarehouse;
   EXEC silver.load_silver;
   ```
3. **Load Gold layer**

   ```sql
   USE DataWarehouse;
   EXEC gold.load_gold;
   ```
4. **Sample Query**

   ```sql
   SELECT d.prd_nm, t.calendar_month, SUM(f.sales_amount) AS total_sales
   FROM gold.fact_sales f
   JOIN gold.dim_product d ON f.product_key = d.product_key
   JOIN gold.dim_time t    ON f.time_key    = t.time_key
   GROUP BY d.prd_nm, t.calendar_month;
   ```

---

## Project Structure

```
sql-data-warehouse-project/
├── datasets/               # Raw CSV files per source system
├── docs/                   # Architecture diagrams (Draw.io) & project notes
├── scripts/                # ETL SQL scripts
│   ├── bronze_load.sql     # Stage raw data (Bronze)
│   ├── silver_load.sql     # Cleanse & transform (Silver)
│   └── gold_load.sql       # Build dimensions & facts (Gold)
├── tests/                  # Validation queries and sample results (including screenshot)
├── LICENSE                 # All rights reserved (or your chosen license)
└── README.md               # This documentation
```

---

## Running the Pipeline

### Ad-Hoc Execution

Run each stored procedure in sequence:

```sql
EXEC bronze.load_bronze;
EXEC silver.load_silver;
EXEC gold.load_gold;
```

### Automated Scheduling (SQL Agent)

Example SQL Agent Job steps:

1. Step 1: `EXEC bronze.load_bronze;`
2. Step 2: `EXEC silver.load_silver;`
3. Step 3: `EXEC gold.load_gold;`

Or, use a batch file (`run_etl.bat`):

```bat
sqlcmd -S . -d DataWarehouse -Q "EXEC bronze.load_bronze;"
sqlcmd -S . -d DataWarehouse -Q "EXEC silver.load_silver;"
sqlcmd -S . -d DataWarehouse -Q "EXEC gold.load_gold;"
```

Schedule the batch via Windows Task Scheduler or Linux cron.

---

## Testing & Validation

Validation scripts in `tests/` perform:

* **Row count checks** across layers
* **Null constraint verifications** on key columns
* **Foreign key integrity tests** between facts and dimensions

Sample output screenshot located at `tests/results.png` demonstrates passing checks.

---

## Contributing

1. ⭐️ Star the repo
2. 🍴 Fork and create a branch (`git checkout -b feature/name`)
3. 🔧 Implement changes and add tests
4. 🔀 Submit a Pull Request with clear descriptions

Refer to `docs/CONTRIBUTING.md` for guidelines and code standards.

---

> *Transforming raw data into actionable insights—layer by layer.*
