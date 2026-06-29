# Healthcare Lakehouse Analytics Platform

## Project Overview

This project implements an end-to-end Healthcare Lakehouse Analytics Platform using Azure Data Lake Storage Gen2 (ADLS Gen2), Azure Databricks, Delta Live Tables (DLT), OMOP Common Data Model (CDM), Unity Catalog, and Databricks SQL Dashboards.

The solution follows the Medallion Architecture (Bronze → Silver → Gold) to ingest, transform, standardize, and analyze healthcare data.

The final output is a Databricks SQL Dashboard powered by Gold KPI tables containing healthcare insights such as ADT Volume, Readmission Rate, Top Diagnoses, Medication Usage, and Average Length of Stay.

---

# Architecture

```
Raw Healthcare Files (ADLS Gen2)

        ↓

Bronze Layer (Ingestion)

        ↓

Silver Layer (Transformation)

        ↓

OMOP CDM Mapping

        ↓

Gold Analytics & KPI Tables

        ↓

Databricks SQL Dashboard

        ↓

Healthcare Insights
```

---

# Tech Stack

* Azure Data Lake Storage Gen2
* Azure Databricks
* Delta Lake
* Delta Live Tables (DLT)
* Unity Catalog
* Databricks Workflows
* Databricks SQL
* PySpark
* SQL
* OMOP CDM v5.4

---

# Setup Instructions

## Prerequisites

Before starting, ensure you have:

* Azure Subscription
* Azure Databricks Workspace
* Azure Data Lake Storage Gen2
* Unity Catalog Enabled
* Databricks SQL Warehouse

---

## Step 1: Create Resource Group

Azure Portal → Resource Groups → Create

Example

```
healthcare-rg
```

---

## Step 2: Create ADLS Gen2 Storage Account

Azure Portal → Storage Accounts → Create

Enable

```
Hierarchical Namespace = Enabled
```

---

## Step 3: Create Storage Containers

Create the following containers:

* raw
* bronze
* silver
* gold

Purpose:

* raw → Source healthcare datasets
* bronze → Raw Delta tables
* silver → Cleaned & standardized datasets
* gold → Analytics-ready datasets

---

## Step 4: Upload Healthcare Files

Upload source healthcare datasets into

```
raw/
```

Example

* patients.csv
* encounters.csv
* conditions.csv
* medications.csv

---

## Step 5: Create Azure Databricks Workspace

Create a Databricks Workspace and attach it to your Resource Group.

---

## Step 6: Create Compute Cluster

Recommended Runtime

* Databricks Runtime 15+
* Standard_DS3_v2 (or equivalent)

---

## Step 7: Configure Unity Catalog

Create

```sql
CREATE CATALOG db_healthcare_kl;

CREATE SCHEMA bronze;
CREATE SCHEMA silver;
CREATE SCHEMA omop;
CREATE SCHEMA gold;
```

---

## Step 8: Connect ADLS Gen2

Configure

* Storage Credential
* External Location

Grant access to

* raw
* bronze
* silver
* gold

---

# Unity Catalog Note

A Unity Catalog bootstrap notebook is included for enterprise deployments.

Azure Student subscriptions do not provide permissions to create a metastore or managed catalog programmatically. Therefore, the catalog and schemas were created manually before development. The bootstrap notebook is provided as a reusable template for enterprise Databricks environments.

---

# Layer 1 — Bronze

## Objective

Ingest raw healthcare datasets into the Lakehouse.

## Features

* Auto Loader ingestion
* Schema inference
* Schema evolution
* Delta tables
* Batch & micro-batch ingestion

## Notebook

```
01_Bronze_Layer
```

## Output Tables

* bronze.patient
* bronze.encounter
* bronze.condition
* bronze.medication

---

# Layer 2 — Silver

## Objective

Clean, standardize, validate and transform healthcare records.

## Features

* Data cleansing
* Null handling
* Deduplication
* Standardized schema
* DLT Expectations
* Data quality validation
* Surrogate key generation

## Notebook

```
02_Silver_Layer
```

## Output Tables

* silver.patient
* silver.encounter
* silver.condition
* silver.medication

---

# OMOP CDM Mapping

## Objective

Map Silver entities into OMOP CDM v5.4.

## Notebook

```
03_OMOP_Mapping
```

## Output Tables

* omop.person
* omop.visit_occurrence
* omop.condition_occurrence
* omop.drug_exposure

---

# Layer 3 — Gold Analytics

## Objective

Create business-ready analytical datasets and KPI tables.

## Notebook

```
04_Gold_Layer
```

## Analytical Tables

* Patient Cohorts
* Encounter Summary
* Readmission Flags

## KPI Tables

* gold.kpi_adt_volume
* gold.kpi_readmission_rate
* gold.kpi_top_diagnoses
* gold.kpi_medication_usage
* gold.kpi_length_of_stay

These Gold KPI tables are the only data sources used by the Databricks SQL Dashboard.

---

# Workflow

The project includes a Databricks Workflow that orchestrates the complete ETL process.

```
Unity Catalog Bootstrap (One-Time Setup)

        ↓

Bronze Layer

        ↓

Silver DLT Pipeline

        ↓

OMOP Mapping

        ↓

Gold Analytics

        ↓

Databricks SQL Dashboard
```

Running the workflow refreshes all Gold KPI tables. Refreshing the dashboard displays the latest analytics.

---

# Governance

## Objective

Implement governance practices for secure, organized, and scalable healthcare analytics.

## Implemented Features

* Unity Catalog for centralized catalog and schema management
* Medallion Architecture (Bronze → Silver → Gold)
* Workflow-based ETL orchestration
* Gold KPI tables as the single source of truth for dashboard reporting
* Structured data organization across Bronze, Silver, OMOP, and Gold schemas

## Enterprise Governance Features

The following governance capabilities are included in the project design but require Azure Databricks Enterprise features that are not available with the Azure Student subscription used for this project.

| Feature                        | Status            | Remarks                                                     |
| ------------------------------ | ----------------- | ----------------------------------------------------------- |
| Unity Catalog Bootstrap Script | ✅ Implemented     | Bootstrap notebook included for enterprise deployment       |
| Column-Level Dynamic Masking   | ❌ Not Implemented | Requires Unity Catalog Enterprise permissions               |
| Row-Level Security             | ❌ Not Implemented | Requires Unity Catalog Enterprise permissions               |
| Databricks Audit Log Pipeline  | ❌ Not Implemented | Requires Databricks system audit logs                       |
| Microsoft Purview Integration  | ❌ Not Implemented | Requires Azure Purview service and enterprise configuration |

The project architecture has been designed so these governance features can be added in an enterprise Databricks environment without modifying the existing Bronze, Silver, OMOP, Gold, Workflow, or Dashboard layers.

--- 

# Clinical KPI Dashboard

The dashboard is built entirely on Gold KPI tables.

## 1. ADT Volume

**Source**

```
gold.kpi_adt_volume
```

Visualization

* Line Chart

---

## 2. Readmission Rate

**Source**

```
gold.kpi_readmission_rate
```

Visualization

* KPI Card

---

## 3. Top Diagnoses

**Source**

```
gold.kpi_top_diagnoses
```

Visualization

* Horizontal Bar Chart

---

## 4. Medication Usage

**Source**

```
gold.kpi_medication_usage
```

Visualization

* Horizontal Bar Chart

---

## 5. Average Length of Stay

**Source**

```
gold.kpi_length_of_stay
```

Visualization

* Bar Chart

---

# Dashboard Components

The dashboard includes

* KPI Cards
* Line Charts
* Bar Charts
* Clinical KPIs
* Interactive Filters
* Healthcare Insights

---

# Folder Structure

```text
Healthcare-Lakehouse-Analytics/

│
├── 00_Unity_Catalog_Bootstrap/
│   └── unity_catalog_bootstrap
│
├── 01_Bronze_Layer/
│   └── bronze_ingestion_notebook
│
├── 02_Silver_Layer/
│   └── silver_dlt_pipeline
│
├── 03_OMOP_Mapping/
│   └── omop_mapping_notebook
│
├── 04_Gold_Layer/
│   └── gold_analytics_notebook
│
├── workflows/
│   └── healthcare_etl_workflow
│
├── dashboards/
│   └── healthcare_dashboard
│
├── screenshots/
│   ├── adt_volume.png
│   ├── readmission_rate.png
│   ├── top_diagnoses.png
│   ├── medication_usage.png
│   └── length_of_stay.png
│
├── README.md
│
└── HIPAA_CONTROLS.md
```

---

# Key Outcomes

* Built an end-to-end Healthcare Lakehouse Analytics Platform
* Implemented Medallion Architecture
* Automated ETL using Delta Live Tables
* Standardized healthcare records using OMOP CDM
* Created Gold analytical datasets
* Built Gold KPI tables
* Automated execution using Databricks Workflow
* Developed Databricks SQL Dashboard
* Generated healthcare insights through interactive analytics

---

# Future Improvements

* ML-based Readmission Prediction
* MLflow Integration
* Model Registry
* Batch Inference Pipeline
* Real-time Streaming Ingestion
* Power BI Integration
* Advanced Cohort Analysis

---

# Author

**Prateek Garg**

Healthcare Lakehouse Analytics Platform

Azure Databricks | Delta Lake | Delta Live Tables | Unity Catalog | OMOP CDM | Databricks SQL | Healthcare Analytics
