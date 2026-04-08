# Databricks HR Payroll End-to-End Pipeline

An automated, governed, and scalable HR Data Platform built on **Databricks Unity Catalog** using the **Medallion Architecture**. This project simulates a real-world payroll system that handles historical tracking (SCD Type 2) and real-time salary updates.

## 🏗 Architecture Overview
The project follows the **Medallion Lakehouse Architecture**:
- **Bronze Layer**: Raw CSV ingestion with audit metadata via Auto Loader.
- **Silver Layer**: Data cleaning, tenure calculations, and Job Title history tracking (SCD Type 2).
- **Gold Layer**: Business-ready aggregates, payroll summaries, and budget tracking.

---

## 🛠 Tech Stack
- **Compute**: Databricks (Spark SQL & PySpark)
- **Storage**: Delta Lake
- **Orchestration**: Databricks Workflows
- **Governance**: Unity Catalog (Row-level filtering & PII Masking)
- **Ingestion**: Auto Loader (`cloudFiles`)
- **Reporting**: Databricks SQL Dashboards

---

## 🚀 Key Features

### 1. Automated Ingestion (Bronze)
Utilizes **Auto Loader** to incrementally process employee updates and payroll files as they arrive in DBFS, ensuring the pipeline is event-driven and scalable.

### 2. Historical Tracking (Silver)
Implements **Slowly Changing Dimensions (SCD) Type 2** for the `job_title` column. 
- Tracks `effective_start_date` and `effective_end_date`.
- Uses a boolean `is_current` flag for quick lookups of active roles.

### 3. Business Logic & Optimization
- **Tenure Calculation**: Calculates years of service dynamically.
- **Z-Order Optimization**: Optimized the Silver layer on `department_id` and `hire_date` to reduce I/O and speed up downstream queries.
- **Change Data Feed (CDF)**: Enabled on critical tables to track row-level changes for auditing.

### 4. Real-time Salary Streams (Gold)
Handles high-frequency salary updates using **Structured Streaming** with a **1-day Watermark** to manage late-arriving data.

### 5. Governance & Security (Unity Catalog)
- **PII Masking**: Masked `full_name` and `email` columns for non-HR admin users.
- **Row-Level Security**: Implemented filters so department managers only see data related to their specific `department_id`.

---

## 📊 Dashboard Insights
The **Gold Layer** feeds a Databricks SQL Dashboard featuring:
- **Total Monthly Payroll**: Bar chart by department and location.
- **Headcount vs. Budget**: Monitoring departments that exceed their allocated hiring budget.

---

## 📅 Pipeline Orchestration
The pipeline is automated via **Databricks Workflows**:
- **Schedule**: 1st of every month at 01:00 AM.
- **Resilience**: Configured with 2 retries on the ingestion task and email alerts for failures.

---

## 📂 Project Structure
```text
├── catalog/                # Catalog and Schema creation scripts
├── Databricks_HR_Project/  # Main Git Folder
│   ├── hr_payroll_pipeline # Main logic notebook (Bronze to Gold)
│   └── README.md           # Project Documentation
└── raw_data/               # CSV source files
