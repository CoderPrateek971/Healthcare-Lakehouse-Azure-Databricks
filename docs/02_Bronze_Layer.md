# Bronze Layer

The Bronze layer is the first stage of the Medallion Architecture and serves as the raw ingestion layer of the Healthcare Lakehouse.

Its primary responsibility is to ingest healthcare data from multiple source systems into Delta Lake without applying business transformations.

The Bronze layer preserves the original data exactly as received, ensuring complete traceability and enabling data reprocessing whenever required.

---

# Purpose

The Bronze layer is responsible for:

- Ingesting raw healthcare datasets
- Supporting multiple healthcare formats
- Preserving original source data
- Providing reliable checkpointing
- Enabling incremental ingestion
- Creating Delta tables for downstream processing

No business logic or data cleaning is performed in this layer.

---

# Input Data Sources

The project supports three healthcare data formats.

## FHIR R4

```
FHIR Bundle JSON
```

Contains resources such as:

- Patient
- Encounter
- Observation
- Condition
- MedicationRequest

---

## HL7 ADT

```
HL7 Admission, Discharge and Transfer Messages
```

Contains hospital operational events.

---

## Flat Files

CSV datasets including

- Patients
- Encounters
- Conditions
- Observations
- Medications

---

# Bronze Architecture

```
Healthcare Files
        │
        ▼
Azure Data Lake Storage (Raw)
        │
        ▼
Auto Loader
        │
        ▼
Schema Tracking
        │
        ▼
Checkpoint Tracking
        │
        ▼
Delta Bronze Tables
```

---

# Auto Loader

The Bronze layer uses **Databricks Auto Loader** for incremental ingestion.

Auto Loader automatically detects:

- Newly arrived files
- Schema changes
- Duplicate processing
- Fault recovery

Only new files are processed during each pipeline execution.

---

# Checkpointing

Each Auto Loader stream maintains its own checkpoint directory.

Example

```
checkpoint/

    bronze_patient/

    bronze_encounter/

    bronze_condition/

    bronze_observation/

    bronze_medication/
```

Checkpointing ensures:

- Exactly-once processing
- Incremental ingestion
- Recovery after failures

---

# Bronze Tables

The following Delta tables are created.

| Table | Description |
|--------|-------------|
| bronze.fhir_raw | Raw FHIR Bundle data |
| bronze.hl7_adt_raw | Raw HL7 ADT messages |
| bronze.patients_raw | Raw patient CSV |
| bronze.encounters_raw | Raw encounter CSV |
| bronze.conditions_raw | Raw condition CSV |
| bronze.observations_raw | Raw observation CSV |
| bronze.medications_raw | Raw medication CSV |

---

# Data Characteristics

The Bronze layer intentionally preserves:

- Original column names
- Original data types
- Original source records

No records are modified.

No deduplication is performed.

No null handling is applied.

---

# Execution Order

The Bronze notebook executes the following ingestion jobs.

1. FHIR Auto Loader
2. HL7 Auto Loader
3. Patient Auto Loader
4. Encounter Auto Loader
5. Condition Auto Loader
6. Observation Auto Loader
7. Medication Auto Loader

Each ingestion process writes independently into its corresponding Delta table.

---

# Output

The Bronze layer produces raw Delta tables that become the input for the Silver layer.

These datasets are stored in the Bronze schema of Unity Catalog and are used for downstream cleaning, standardization, and analytics.

---

# Pipeline Integration

The Bronze notebook is executed automatically through the **Healthcare Lakehouse Daily Refresh** workflow.

No manual execution is required after the initial deployment.

---

# Troubleshooting

## Auto Loader cannot detect files

Verify:

- Source path exists
- Files are placed inside the correct directory
- Checkpoint directory is unique for each stream

---

## Schema mismatch

Ensure that each Auto Loader writes to its own dedicated Delta table.

Do not ingest multiple datasets into the same Bronze table.

---

## Input path is not a directory

Verify that the configured source path points to a folder rather than a single file.

Example:

✔ Correct

```
raw/flat files/patients/
```

✘ Incorrect

```
raw/flat files/patients.csv
```

---

# Next Step

After successful ingestion, the Silver layer extracts healthcare resources, cleans the data, standardizes schemas, merges multiple healthcare formats, and prepares analytics-ready datasets.