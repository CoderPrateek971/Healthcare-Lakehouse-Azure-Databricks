# Gold Layer

The Gold layer is the business analytics layer of the Medallion Architecture.

It transforms standardized OMOP datasets into business-ready tables that support healthcare analytics, KPI reporting, dashboards, and downstream applications.

Unlike the Bronze and Silver layers, the Gold layer focuses on generating aggregated and reporting-friendly datasets rather than preserving transactional healthcare records.

---

# Purpose

The Gold layer is responsible for:

- Generating healthcare KPIs
- Creating patient cohorts
- Creating encounter summaries
- Supporting business analytics
- Preparing dashboard-ready datasets

---

# Why Gold Layer?

The Bronze layer stores raw healthcare data.

↓

The Silver layer cleans and standardizes the data.

↓

The OMOP layer converts healthcare data into a common data model.

↓

The Gold layer transforms standardized healthcare data into meaningful business insights for reporting and visualization.

---

# Gold Architecture

```
OMOP Tables

Person_Data

Visit_Occurrence

Condition_Occurrence

Drug_Exposure

Observation

        │

        ▼

Business Logic

        │

        ▼

Healthcare KPIs

Patient Cohorts

Encounter Summaries

Dashboard Tables
```

---

# Input Tables

The Gold layer reads the following OMOP tables.

| OMOP Table | Description |
|------------|-------------|
| omop.person_data | Patient demographic information |
| omop.visit_occurrence | Hospital encounters |
| omop.condition_occurrence | Diagnoses |
| omop.drug_exposure | Medication history |
| omop.observation | Clinical observations |

---

# Gold Tables

The notebook creates the following Gold datasets.

| Gold Table | Description |
|------------|-------------|
| gold.encounter_summaries | Summary of each patient encounter |
| gold.patient_cohorts | Patient cohort information |
| gold.readmission_within_30days | 30-day readmission information |
| gold.healthcare_kpis | Healthcare KPI dataset |

---

# Business Transformations

The Gold layer applies healthcare-specific business logic.

Examples include:

- Patient age calculation
- Length of stay calculation
- Previous admission count
- Readmission calculation
- Diagnosis aggregation
- Medication aggregation

---

# Encounter Summary

Each hospital encounter is summarized into a single business record.

Example

```
Visit

↓

Length of Stay

↓

Diagnosis Count

↓

Medication Count

↓

Encounter Summary
```

The resulting dataset provides a concise overview of every patient encounter for reporting and analysis.

---

# Patient Cohorts

Patients are grouped into cohorts based on common healthcare characteristics.

Examples include:

- Age Group
- Gender
- Disease Category
- Admission Type

Patient cohorts enable population-level analysis and simplify dashboard reporting.

---

# Readmission Information

The Gold layer determines whether a patient was readmitted within 30 days.

Logic

```
Current Encounter

↓

Next Encounter

↓

Difference ≤ 30 Days

↓

Readmitted

Yes / No
```

This produces the table

```
gold.readmission_within_30days
```

which is later used by the Machine Learning pipeline.

---

# Healthcare KPIs

The Gold layer computes business metrics including:

- Total Patients
- Total Admissions
- Readmission Rate
- Average Length of Stay
- Top Diagnoses

These KPIs are used directly in dashboards and reports.

---

# Dashboard Integration

The Gold tables serve as the primary data source for healthcare dashboards.

```
Gold Tables

        │

        ▼

Databricks SQL

        │

        ▼

Power BI / Databricks Dashboard
```

The dashboards display:

- Patient Statistics
- Admission Trends
- Readmission Rate
- Average Length of Stay
- Top Diagnoses

---

# Pipeline Integration

The Gold layer executes after the OMOP Mapping layer.

Pipeline execution order

```
Bronze

↓

Silver

↓

OMOP Mapping

↓

Gold Layer

↓

Machine Learning

↓

Model Inference
```

---

# Output

The Gold layer produces business-ready Delta tables that support:

- Healthcare Analytics
- Executive Reporting
- Dashboard Visualization
- Business Intelligence

---

# Benefits

The Gold layer provides:

- Dashboard-ready datasets
- Business-friendly schemas
- Healthcare KPIs
- Patient cohort analysis
- Faster analytical queries
- Standardized reporting

---

# Troubleshooting

## Missing KPIs

Verify that all OMOP tables have been populated successfully before executing the Gold notebook.

---

## Incorrect Readmission Counts

Ensure that encounter dates are correctly sorted before calculating the 30-day readmission information.

---

## Empty Dashboard

Verify that:

- Gold tables contain data.
- Dashboard queries reference the correct catalog and schema.
- Databricks SQL Warehouse is running.

---

# Next Step

The Gold tables generated in this notebook are consumed by:

- Machine Learning Pipeline for model training
- Model Inference Pipeline for prediction
- Healthcare Dashboards for business reporting