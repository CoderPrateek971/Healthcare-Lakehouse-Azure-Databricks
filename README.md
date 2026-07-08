# Healthcare Lakehouse Analytics Platform

## Project Overview

This project implements an end-to-end Healthcare Lakehouse Analytics Platform using Azure Data Lake Storage Gen2 (ADLS Gen2), Azure Databricks, Delta Live Tables (DLT), OMOP Common Data Model (CDM), Unity Catalog, and Databricks SQL Dashboards.

The solution follows the Medallion Architecture (Bronze → Silver → OMOP → Gold) to ingest, transform, standardize, and analyze healthcare data from multiple healthcare data formats.

The platform supports ingestion of:

- FHIR R4 Resources (JSON)
- HL7 Flat Files (CSV)
- ADT (Admission, Discharge and Transfer) Records

All healthcare data is standardized into the OMOP Common Data Model (CDM) before generating Gold analytical datasets and Databricks SQL dashboards.

The final output is a Databricks SQL Dashboard powered by Gold KPI tables containing healthcare insights such as ADT Volume, Readmission Rate, Top Diagnoses, Medication Usage, and Average Length of Stay.

---

# Architecture

```text
FHIR JSON
HL7 Flat Files
ADT Records

        ↓

ADLS Gen2 (Raw)

        ↓

Bronze Layer

        ↓

Silver Layer (DLT)

        ↓

OMOP CDM

        ↓

Gold Layer

├── KPI Tables
│       ↓
│  SQL Dashboard
│
└── ML Features
        ↓
 Model Training
        ↓
 MLflow + Model Registry
        ↓
 Batch Inference
        ↓
 Prediction Table
```

---

# Multi-Source Data Integration

The platform ingests healthcare data from multiple sources.

Input Sources

- FHIR JSON
- HL7 Flat Files
- Clinical CSV Files
- ADT Records

These heterogeneous datasets are standardized into unified Silver business entities before being mapped into the OMOP Common Data Model for downstream analytics.

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
* FHIR R4
* HL7
* ADT Healthcare Data
* Auto Loader

---

# Supported Healthcare Standards

| Standard | Description |
|----------|-------------|
| FHIR R4 | REST-based Healthcare Interoperability Standard |
| HL7 | Legacy Healthcare Messaging Standard |
| ADT | Admission, Discharge and Transfer Events |
| OMOP CDM v5.4 | Standardized Clinical Data Model |

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

FHIR Resources

- Patient.json
- Encounter.json
- Condition.json
- Observation.json
- MedicationRequest.json

HL7 Flat Files

- patient.csv
- encounter.csv

ADT Records

- adt_events.csv

Clinical Flat Files

- conditions.csv
- observations.csv
- medications.csv

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

Ingest multiple healthcare data formats into the Lakehouse while preserving raw data.

Supported Sources

- FHIR JSON
- HL7 Flat Files
- ADT Records
- Clinical CSV Files

## Features

* Multi-format ingestion
* Auto Loader
* Schema inference
* Schema evolution
* Delta conversion
* Delta tables
* Batch & micro-batch ingestion

## Notebook

```
01_Bronze_Layer
```

## Output Tables

* bronze.patient_fhir
* bronze.encounter_fhir
* bronze.condition_fhir
* bronze.observation_fhir
* bronze.medicationrequest_fhir
* bronze.patient_hl7
* bronze.encounter_hl7
* bronze.patient_flat
* bronze.encounter_flat
* bronze.condition_flat
* bronze.observation_flat
* bronze.medication_flat

---

# Layer 2 — Silver

## Objective

The Silver layer standardizes healthcare records coming from multiple data sources into one unified business entity.

Multiple input formats are merged into a single Silver table using schema alignment and union operations. Clean, standardize, validate and transform healthcare records.

## Features

* Multi-source integration
* Schema standardization
* Union across FHIR, HL7 and Flat Files
* Data cleansing
* Null handling
* Deduplication
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
* silver.observation
* silver.medication_request

---

# OMOP CDM Mapping

## Objective

Map standardized healthcare entities from FHIR, HL7 and Flat Files into OMOP CDM v5.4.

## Notebook

```
03_OMOP_Mapping
```

## Output Tables

* omop.person
* omop.visit_occurrence
* omop.condition_occurrence
* omop.drug_exposure
* omop.observation_occurrence

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

# Machine Learning Pipeline

## Objective

Predict the likelihood of a patient being readmitted within 30 days using healthcare features generated in the Gold layer.

The machine learning pipeline uses Gold analytical data to train an XGBoost classifier, tracks experiments with MLflow, registers the best model in Unity Catalog Model Registry, and performs batch inference on newly processed patient cohorts.

---

# Model Training

## Notebook

```
05_ML_Readmission_Prediction
```

## Objective

Train an XGBoost model using Gold layer features and register the best-performing model.

## Workflow

```text
Gold Readmission Features

        ↓

Feature Engineering

        ↓

Categorical Encoding

        ↓

Vector Assembler

        ↓

Train/Test Split

        ↓

XGBoost Training

        ↓

Model Evaluation

        ↓

MLflow Tracking

        ↓

Unity Catalog Model Registry

        ↓

Champion Model
```

## Features

* Uses Gold analytical dataset as training data
* Performs categorical feature encoding using StringIndexer
* Creates feature vectors using VectorAssembler
* Trains an XGBoost binary classification model
* Evaluates model performance using classification metrics
* Logs experiments using MLflow
* Registers the model in Unity Catalog Model Registry
* Saves preprocessing artifacts inside Unity Catalog Volumes for inference

## Output

```
Model Registry

readmission_xgboost_model
```

Preprocessing artifacts stored in

```
/Volumes/db_healthcare_kl/default/ml_models/
```

including

* StringIndexer Models
* Feature metadata

---

# Batch Model Inference

## Notebook

```
06_Model_Inference
```

## Objective

Run batch predictions on newly processed patient cohorts using the Champion model registered in MLflow.

## Workflow

```text
Gold Readmission Features

        ↓

Latest Patient Cohort

        ↓

Load Champion Model

        ↓

Apply Same Feature Engineering

        ↓

Batch Prediction

        ↓

Prediction Table
```

## Features

* Loads the Champion model from Unity Catalog Model Registry
* Reads the latest patient cohort from the Gold layer
* Applies the same preprocessing pipeline used during training
* Generates readmission predictions
* Stores prediction results as a Gold table
* Displays predictions for downstream analytics

## Output Table

```
gold.readmission_predictions
```

---

# Workflow

The project includes a Databricks Workflow that orchestrates the complete ETL process.

```text
Unity Catalog Bootstrap (One-Time Setup)

        ↓

FHIR
HL7
Flat Files

        ↓

Bronze

        ↓

Silver DLT

        ↓

OMOP

        ↓

Gold

├── Dashboard

└── ML Training
        ↓
Model Registry
        ↓
Batch Inference
```

Running the workflow refreshes all Gold KPI tables. Refreshing the dashboard displays the latest analytics.

---

# Governance

## Objective

Implement governance practices for secure, organized, and scalable healthcare analytics.

## Implemented Features

* Unity Catalog for centralized catalog and schema management
* Medallion Architecture (Bronze → Silver → OMOP → Gold)
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
├── 05_ML_Readmission_Prediction/
│   └── model_training_notebook
│
├── 06_Model_Inference/
│   └── batch_inference_notebook
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

* Built an end-to-end Multi-Source Healthcare Lakehouse Analytics Platform
* Integrated healthcare data from
  - FHIR
  - HL7
  - Flat Files
* Implemented Medallion Architecture
* Standardized healthcare records using OMOP CDM
* Automated ETL using Delta Live Tables
* Created Gold KPI Tables
* Developed interactive Databricks SQL Dashboard
* Implemented Workflow Orchestration
* Generated healthcare insights from multiple healthcare standards
* Built an end-to-end healthcare readmission prediction pipeline
* Trained an XGBoost model using Gold layer features
* Tracked experiments with MLflow
* Registered the production model in Unity Catalog Model Registry
* Implemented batch inference for newly processed patient cohorts
* Stored prediction results as Gold tables for downstream analytics

---

# Future Improvements

* FHIR Streaming APIs
* HL7 Message Streaming
* CDC Incremental Ingestion
* Patient 360 Dashboard
* Clinical Risk Prediction
* Real-time Streaming Ingestion
* Power BI Integration
* Advanced Cohort Analysis
* Hyperparameter Optimization
* Automated Model Retraining
* Real-time Online Inference
* Model Monitoring and Drift Detection
* Feature Store Integration
* Explainable AI (SHAP)
* Scheduled ML Workflows

---

# Author

**Prateek Garg**

Healthcare Lakehouse Analytics Platform

Azure Databricks | Delta Lake | Delta Live Tables | Unity Catalog | OMOP CDM | Databricks SQL | Healthcare Analytics