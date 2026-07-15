# 🏥 Healthcare Lakehouse on Azure Databricks

An end-to-end Healthcare Lakehouse built on **Azure Databricks** using the **Medallion Architecture (Bronze → Silver → Gold)** with **FHIR R4**, **HL7**, and **Flat File** ingestion, **OMOP Common Data Model**, **Machine Learning**, **Databricks Workflows**, and **Interactive Dashboards**.

---

## 📌 Project Overview

This project demonstrates how modern healthcare data from multiple sources can be ingested, standardized, transformed, analyzed, and used for machine learning predictions within a scalable Lakehouse architecture.

The pipeline supports:

- 📥 Multi-format healthcare ingestion
  - FHIR R4 JSON
  - HL7 ADT Messages
  - CSV Flat Files

- 🥉 Bronze Layer
  - Raw data ingestion
  - Auto Loader
  - Delta Lake

- 🥈 Silver Layer
  - Data Cleaning
  - Schema Standardization
  - Resource Extraction
  - Data Validation
  - Merging Multiple Healthcare Formats

- 🩺 OMOP CDM Mapping
  - Standardized healthcare model

- 🥇 Gold Layer
  - Business KPIs
  - Analytics Tables
  - Dashboard-ready datasets

- 🤖 Machine Learning
  - 30-Day Readmission Prediction

- ⚡ Model Inference
  - Predict only newly arrived patients

- 📊 Interactive Dashboard

- 🔄 Databricks Workflows

---

# 🏗 Architecture

![Architecture](docs/Architecture.png)

---

# 🚀 Technology Stack

| Category | Technologies |
|-----------|-------------|
| Cloud | Microsoft Azure |
| Storage | Azure Data Lake Storage Gen2 |
| Compute | Azure Databricks |
| Processing | Apache Spark (PySpark) |
| Format | Delta Lake |
| Data Model | OMOP CDM v5.4 |
| Workflow | Databricks Jobs |
| Dashboard | Databricks SQL Dashboard |
| ML | XGBoost |
| Model Registry | MLflow |
| Language | Python |

---

# 📂 Repository Structure

```text
Healthcare-Lakehouse/
│
├── README.md
│
├── docs/
│   ├── 01_Azure_Setup.md
│   ├── 02_Bronze_Layer.md
│   ├── 03_Silver_Layer.md
│   ├── 04_OMOP_Mapping.md
│   ├── 05_Gold_Layer.md
│   ├── 06_ML_Pipeline.md
│   ├── 07_Model_Inference.md
│   ├── 08_Workflows.md
│   ├── 09_Dashboard.md
│   ├── 10_Troubleshooting.md
│   └── Architecture.png
│
├── notebooks/
│
├── workflows/
│
├── dashboards/

```

---

# 📚 Documentation

Complete documentation is available inside the **docs** folder.

| Document | Description |
|----------|-------------|
| 📘 Azure Setup | Complete Azure resource setup |
| 🥉 Bronze Layer | Raw data ingestion |
| 🥈 Silver Layer | Cleaning & Standardization |
| 🩺 OMOP Mapping | OMOP CDM transformation |
| 🥇 Gold Layer | Business analytics |
| 🤖 ML Pipeline | Model training |
| ⚡ Model Inference | Batch inference |
| 🔄 Workflows | Databricks Jobs |
| 📊 Dashboard | Dashboard setup |
| 🛠 Troubleshooting | Common issues |

---

# ▶ Quick Start

Follow the documentation in the following order:

1. Azure Setup
2. Bronze Layer
3. Silver Layer
4. OMOP Mapping
5. Gold Layer
6. Machine Learning
7. Model Inference
8. Import Workflows
9. Import Dashboard

---

# 📥 Import Databricks Workflows

Workflow JSON files are available inside:

```

workflows/

```

Refer to:

```

docs/08_Workflows.md

```

for the complete import process.

---

# 📊 Import Dashboard

Dashboard JSON files are available inside:

```

dashboards/

```

Refer to:

```

docs/09_Dashboard.md

```

for dashboard import instructions.

---

# 📸 Project Screenshots

Project screenshots are available in the **screenshots/** directory.

Example screenshots include:

- Architecture
- Bronze Layer
- Silver Layer
- Gold Layer
- Workflows
- Dashboard
- Machine Learning
- Model Inference

---

# ⭐ Features

- End-to-End Healthcare Lakehouse
- Medallion Architecture
- Auto Loader
- Delta Lake
- Unity Catalog
- OMOP Common Data Model
- Machine Learning Pipeline
- MLflow Model Registry
- Batch Inference
- Databricks Workflows
- Dashboard-ready Gold Layer

---

# 📖 License

This project is intended for educational and learning purposes.

---

# 👤 Author

**Prateek Garg**

B.Tech Computer Science Engineering

Azure Databricks • PySpark • Delta Lake • Machine Learning • Healthcare Analytics
