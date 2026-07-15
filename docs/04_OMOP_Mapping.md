# OMOP Common Data Model (CDM) Mapping

The OMOP Common Data Model (CDM) is a standardized healthcare data model developed by the **Observational Health Data Sciences and Informatics (OHDSI)** community.

It enables healthcare data from multiple systems to be stored in a common format, making it easier to perform analytics, machine learning, and research across different organizations.

In this project, the standardized Silver layer datasets are transformed into OMOP CDM tables.

---

# Purpose

The OMOP Mapping layer is responsible for:

- Converting healthcare data into a standard data model
- Mapping Silver datasets to OMOP tables
- Standardizing healthcare entities
- Improving interoperability
- Preparing data for analytics and machine learning

---

# Why OMOP?

Healthcare systems store data in different formats.

Examples include:

- FHIR
- HL7
- CSV Flat Files
- Hospital Information Systems

Each system has its own schema.

OMOP provides a single standardized schema that enables all healthcare data to be analyzed consistently.

---

# OMOP Architecture

```
Silver Layer

Patient

Encounter

Condition

Observation

Medication

        │

        ▼

Field Mapping

        │

        ▼

OMOP CDM

        │

        ▼

person_data

visit_occurrence

condition_occurrence

drug_exposure

observation
```

---

# Input Tables

The OMOP Mapping notebook reads the following Silver tables.

| Silver Table | Description |
|--------------|-------------|
| silver.patient | Standardized patient dataset |
| silver.encounter | Standardized encounter dataset |
| silver.condition | Standardized condition dataset |
| silver.observation | Standardized observation dataset |
| silver.medication | Standardized medication dataset |

---

# Output Tables

The notebook creates the following OMOP tables.

| OMOP Table | Description |
|------------|-------------|
| omop.person_data | Patient demographic information |
| omop.visit_occurrence | Hospital encounters |
| omop.condition_occurrence | Diagnoses and medical conditions |
| omop.drug_exposure | Medication history |
| omop.observation | Clinical observations |

---

# Mapping Process

Each Silver dataset is transformed into its corresponding OMOP table.

Example

```
Silver Patient

        │

        ▼

Column Mapping

        │

        ▼

OMOP person_data
```

---

## Patient Mapping

Silver Patient

↓

OMOP person_data

Example mappings

| Silver Column | OMOP Column |
|---------------|-------------|
| patient_id | person_id |
| birth_date | birth_datetime |
| gender | gender_concept_id |

---

## Encounter Mapping

Silver Encounter

↓

OMOP Visit Occurrence

Example mappings

| Silver Column | OMOP Column |
|---------------|-------------|
| encounter_id | visit_occurrence_id |
| patient_reference | person_id |
| start_date | visit_start_datetime |
| end_date | visit_end_datetime |

---

## Condition Mapping

Silver Condition

↓

OMOP Condition Occurrence

Example mappings

| Silver Column | OMOP Column |
|---------------|-------------|
| condition_id | condition_occurrence_id |
| patient_reference | person_id |
| encounter_reference | visit_occurrence_id |
| onset_date | condition_start_datetime |

---

## Medication Mapping

Silver Medication

↓

OMOP Drug Exposure

Example mappings

| Silver Column | OMOP Column |
|---------------|-------------|
| medication_id | drug_exposure_id |
| patient_reference | person_id |
| encounter_reference | visit_occurrence_id |

---

## Observation Mapping

Silver Observation

↓

OMOP Observation

Example mappings

| Silver Column | OMOP Column |
|---------------|-------------|
| observation_id | observation_id |
| patient_reference | person_id |
| encounter_reference | visit_occurrence_id |
| observation_date | observation_datetime |
| value | value_as_string |

---

# Data Standardization

The OMOP layer ensures:

- Consistent column names
- Standard healthcare entities
- Uniform relationships between patients, encounters, conditions, and medications

This makes downstream analytics significantly easier.

---

# Benefits of OMOP

Using OMOP provides several advantages:

- Healthcare interoperability
- Standardized analytics
- Simplified machine learning
- Reusable SQL queries
- Easier reporting
- Research-ready datasets

---

# Pipeline Integration

The OMOP Mapping notebook executes automatically after the Silver layer.

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
```

---

# Output

The OMOP tables become the primary source for:

- Gold Layer aggregations
- KPI generation
- Machine Learning feature engineering
- Readmission prediction
- Dashboard reporting

---

# Troubleshooting

## Missing Patient Records

Verify that:

- Silver Patient table contains valid patient IDs.
- Person mapping completed successfully.

---

## Missing Encounter References

Ensure that encounter identifiers exist before mapping to **visit_occurrence**.

---

## Null Values

Check for missing mandatory fields before writing to OMOP tables.

---

## Schema Mismatch

Verify that the Silver schema matches the expected OMOP mapping before executing the notebook.

---

# Next Step

The standardized OMOP tables are used to build the **Gold Layer**, where healthcare KPIs, feature tables, encounter summaries, patient cohorts, and machine learning datasets are generated for reporting and predictive analytics.