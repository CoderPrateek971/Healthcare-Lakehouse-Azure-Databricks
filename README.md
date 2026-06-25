# Healthcare Lakehouse Analytics Platform

## Project Overview

This project implements an end-to-end Healthcare Lakehouse Analytics Platform using Azure Data Lake Storage Gen2 (ADLS Gen2), Azure Databricks, Delta Live Tables (DLT), OMOP Common Data Model (CDM), and Databricks SQL Dashboards.

The solution follows the Medallion Architecture (Bronze → Silver → Gold) to ingest, transform, standardize, and analyze healthcare data.

The final output is a Databricks SQL Dashboard containing clinical KPIs such as ADT Volume, Readmission Rate, Top Diagnoses, Medication Usage, and Length of Stay.

---

# Architecture

Raw Healthcare Files (ADLS Gen2)

↓

Bronze Layer (Ingestion)

↓

Silver Layer (Transformation)

↓

OMOP CDM Mapping

↓

Gold Analytics Layer

↓

Databricks SQL Dashboard

↓

Healthcare Insights

---

# Tech Stack

* Azure Data Lake Storage Gen2
* Azure Databricks
* Delta Lake
* Delta Live Tables (DLT)
* Unity Catalog
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

Example:

Resource Group Name:

healthcare-rg

---

## Step 2: Create ADLS Gen2 Storage Account

Azure Portal → Storage Accounts → Create

Configuration:

Storage Account Name:

healthcareadls

Enable:

Hierarchical Namespace = Enabled

---

## Step 3: Create Containers

Create the following containers:

raw

bronze

silver

gold

Purpose:

raw → Source healthcare datasets

bronze → Raw Delta tables

silver → Cleaned and transformed data

gold → Analytics-ready datasets

---

## Step 4: Upload Healthcare Files

Upload source files into:

raw/

Example datasets:

* patients.csv
* encounters.csv
* conditions.csv
* medications.csv

---

## Step 5: Create Azure Databricks Workspace

Azure Portal → Azure Databricks → Create

Attach it to:

healthcare-rg

---

## Step 6: Create Compute Cluster

Inside Databricks:

Compute → Create Compute

Configuration:

* Databricks Runtime 15+
* Standard_DS3_v2 (or equivalent)

---

## Step 7: Configure Unity Catalog

Create Catalog:

```sql
CREATE CATALOG db_healthcare_kl;
```

Create Schemas:

```sql
CREATE SCHEMA bronze;
CREATE SCHEMA silver;
CREATE SCHEMA omop;
CREATE SCHEMA gold;
```

---

## Step 8: Connect ADLS Gen2

Configure Storage Credential and External Location.

Grant access to the Databricks workspace.

Verify access to:

* raw container
* bronze container
* silver container
* gold container

---

# Layer 1 — Bronze: Ingestion

## Objective

Ingest raw healthcare datasets into the Lakehouse.

## Features

* Auto Loader ingestion
* Schema inference
* Schema evolution
* Delta table storage
* Batch and micro-batch ingestion

## Notebook

01_Bronze_Layer

## Output Tables

* bronze.patient
* bronze.encounter
* bronze.condition
* bronze.medication

---

# Layer 2 — Silver: Transformation

## Objective

Clean, standardize, and validate healthcare records.

## Features

* Data cleansing
* Null handling
* Deduplication
* Standardized column naming
* Data quality checks
* DLT expectations
* Surrogate key generation

## Notebook

02_Silver_Layer

## Processed Entities

### Patient

Demographics and patient information

### Encounter

Visit and admission information

### Condition

Clinical diagnoses

### Medication

Medication prescriptions and exposures

## Output Tables

* silver.patient
* silver.encounter
* silver.condition
* silver.medication

---

# OMOP CDM Mapping

## Objective

Map Silver entities to OMOP Common Data Model v5.4.

## Notebook

03_OMOP_Mapping

## OMOP Tables

* omop.person
* omop.visit_occurrence
* omop.condition_occurrence
* omop.drug_exposure

---

# Layer 3 — Gold: Analytics

## Objective

Create business-ready analytical datasets.

## Notebook

04_Gold_Layer

## Gold Tables

### Patient Cohorts

Patient population analytics

### Encounter Summary

Visit statistics and utilization metrics

### Readmission Flags

30-day readmission indicators

---

# Clinical KPI Dashboard

Databricks SQL Dashboard containing five healthcare KPIs.

## 1. ADT Volume

Total healthcare encounters.

Example:

27.81K Visits

Visualization:

KPI Card

---

## 2. Readmission Rate

Percentage of patients readmitted within 30 days.

Example:

39.02%

Visualization:

KPI Card

---

## 3. Top Diagnoses

Most frequently occurring clinical conditions.

Visualization:

Bar Chart

---

## 4. Medication Usage

Most commonly prescribed medications.

Visualization:

Bar Chart

---

## 5. Length of Stay

Average stay duration by visit type.

Visit Types:

* Ambulatory
* Emergency
* Inpatient

Visualization:

Bar Chart

---

# Dashboard Components

The dashboard includes:

* KPI Cards
* Bar Charts
* Healthcare Metrics
* Clinical Insights
* Interactive Filters

---

# Folder Structure

```text
Healthcare-Lakehouse-Analytics/

│
├── 01_Bronze_Layer/
│   └── bronze_ingestion_notebook
│
├── 02_Silver_Layer/
│   └── silver_transformation_notebook
│
├── 03_OMOP_Mapping/
│   └── omop_mapping_notebook
│
├── 04_Gold_Layer/
│   └── analytics_queries
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
└── README.md
```

---

# Key Outcomes

* Built an end-to-end Healthcare Lakehouse platform
* Implemented Medallion Architecture
* Standardized healthcare records using OMOP CDM
* Created analytical Gold datasets
* Developed Databricks SQL Dashboard
* Generated clinical KPIs and insights

---

# Future Improvements

* Real-time streaming ingestion
* Predictive readmission risk modeling
* Clinical outcome analytics
* Power BI integration
* Advanced cohort analysis

---

# Author

Prateek Garg

Healthcare Lakehouse Analytics Platform

Azure Databricks | Delta Lake | DLT | OMOP CDM | SQL Analytics
