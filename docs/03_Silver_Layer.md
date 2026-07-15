# Silver Layer

The Silver layer is the transformation layer of the Medallion Architecture.

Its primary responsibility is to convert raw healthcare data into clean, standardized, and analytics-ready datasets.

Unlike the Bronze layer, the Silver layer applies business logic, extracts healthcare resources, standardizes schemas, validates records, and merges data from multiple healthcare formats into unified datasets.

---

# Purpose

The Silver layer performs the following tasks:

- Extract healthcare resources
- Parse FHIR Bundles
- Standardize schemas
- Clean raw data
- Merge multiple healthcare formats
- Validate records
- Remove duplicate records
- Create analytics-ready Delta tables

---

# Input Tables

The Silver layer reads the following Bronze tables.

| Bronze Table | Description |
|--------------|-------------|
| bronze.fhir_raw | Raw FHIR Bundle data |
| bronze.hl7_adt_raw | Raw HL7 ADT Messages |
| bronze.patients_raw | Raw Patient CSV |
| bronze.encounters_raw | Raw Encounter CSV |
| bronze.conditions_raw | Raw Condition CSV |
| bronze.observations_raw | Raw Observation CSV |
| bronze.medications_raw | Raw Medication CSV |

---

# Silver Architecture

```
Bronze Layer

FHIR
HL7
Flat Files

        │
        ▼

Cleaning

        │

Schema Standardization

        │

Healthcare Resource Extraction

        │

Merge Multiple Sources

        │

Validation

        │

Deduplication

        │

Silver Tables
```

---

# Healthcare Resource Extraction

FHIR stores healthcare resources inside an array named **entry**.

The Silver layer explodes this array to obtain individual healthcare resources.

Example resources include:

- Patient
- Encounter
- Observation
- Condition
- MedicationRequest

Each resource is extracted independently before further transformation.

---

# FHIR Bundle Processing

```
FHIR Bundle

        │

        ▼

explode(entry)

        │

        ▼

Patient

Encounter

Condition

Observation

MedicationRequest
```

Each extracted resource is filtered and transformed into its corresponding Silver dataset.

---

# Data Cleaning

The Silver layer performs data cleaning operations such as:

- Removing invalid records
- Handling missing values
- Standardizing null values
- Casting columns to appropriate data types
- Removing unnecessary nested fields

No business aggregations are performed in this layer.

---

# Schema Standardization

Healthcare data arrives from different systems.

Each source uses different column names and formats.

The Silver layer converts every source into a common schema.

Example

Patient

FHIR

↓

Patient

HL7

↓

Patient

Flat File

↓

Common Patient Schema

---

# Merge Multiple Healthcare Sources

The project supports three healthcare formats.

- FHIR
- HL7
- Flat Files

After standardization, datasets are merged using

```
unionByName()
```

Example

```
FHIR Patient

        +

HL7 Patient

        +

Flat File Patient

        │

        ▼

patient_silver_df
```

The same process is applied for

- Encounter
- Condition
- Observation
- Medication

This produces one unified dataset for each healthcare entity.

---

# Silver Tables

The following Silver Delta tables are created.

| Silver Table | Description |
|---------------|-------------|
| silver.patient | Standardized patient dataset |
| silver.encounter | Standardized encounter dataset |
| silver.condition | Standardized condition dataset |
| silver.observation | Standardized observation dataset |
| silver.medication | Standardized medication dataset |
| silver.provider | Provider information |
| silver.location | Location information |
| silver.organization | Organization information |

---

# Data Validation

The Silver layer validates incoming records before writing them to Delta tables.

Validation examples include:

- Valid Patient ID
- Valid Encounter ID
- Valid Dates
- Required Fields Present
- Valid Resource Types

Invalid records are excluded from downstream analytics.

---

# Deduplication

Duplicate records originating from multiple healthcare systems are removed.

Deduplication is performed using unique healthcare identifiers whenever available.

This ensures that each healthcare entity appears only once in the Silver layer.

---

# Output

The Silver layer produces clean and standardized Delta tables.

These tables become the primary input for:

- OMOP Mapping
- Gold Layer
- Machine Learning
- Analytics
- Dashboard

---

# Pipeline Integration

The Silver layer executes automatically after the Bronze layer completes successfully.

Execution Order

```
Bronze Layer

↓

Silver Layer

↓

OMOP Mapping

↓

Gold Layer
```

---

# Benefits of the Silver Layer

The Silver layer provides:

- Clean healthcare datasets
- Unified schemas
- Standardized healthcare resources
- Reliable downstream processing
- Improved data quality
- Reduced duplicate records

---

# Troubleshooting

## Resource Not Found

Verify that the FHIR Bundle contains the required resource type.

Example

- Patient
- Encounter
- Observation
- Condition
- MedicationRequest

---

## Schema Mismatch

Ensure that all input datasets are standardized before applying

```
unionByName()
```

---

## Duplicate Records

Verify that duplicate removal is performed using the appropriate healthcare identifier.

---

## Missing Columns

Check that all healthcare sources have been mapped to the common Silver schema before merging.

---

# Next Step

The standardized Silver datasets are transformed into the **OMOP Common Data Model (CDM)** to enable interoperable healthcare analytics and downstream business reporting.