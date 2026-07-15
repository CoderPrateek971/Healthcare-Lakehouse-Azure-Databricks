# Troubleshooting

This document provides solutions to the most common issues that may occur while deploying or running the Healthcare Lakehouse project.

The issues are organized by pipeline stage to simplify debugging and reduce downtime.

---

# Azure Setup Issues

## Storage Access Failed

### Possible Causes

- Incorrect Storage Account name
- Incorrect Container name
- Invalid ABFS path
- Missing permissions

### Solution

- Verify the Storage Account exists.
- Verify the Container exists.
- Check the ABFS path.
- Ensure the Databricks Managed Identity has **Storage Blob Data Contributor** permission.

---

## Secret Scope Not Found

Error

```
Secret Scope does not exist
```

### Solution

Verify that:

- Secret Scope has been created.
- Secret names match those used in the notebooks.
- Workspace permissions allow secret access.

---

## Unity Catalog Permission Denied

### Solution

Verify that:

- Unity Catalog is enabled.
- Storage Credential exists.
- External Location exists.
- Required permissions have been granted.

---

# Bronze Layer Issues

## Auto Loader Reads No Files

### Possible Causes

- Empty Raw folder
- Incorrect source path
- Checkpoint conflict

### Solution

- Verify files exist in the Raw container.
- Check the configured ABFS path.
- Delete the checkpoint only if reprocessing is required.

---

## Schema Inference Failed

### Solution

Verify that:

- Uploaded files are valid.
- File format matches the configured reader.
- Corrupted files are removed.

---

# Silver Layer Issues

## Missing FHIR Resources

### Possible Causes

- Incorrect resource filtering
- Invalid JSON structure

### Solution

Verify that:

- The `resourceType` field exists.
- The correct resource name is being filtered.

---

## Union Failed

Error

```
Union can only be performed on tables with compatible schemas.
```

### Solution

Ensure that:

- Column names match.
- Data types match.
- Missing columns are added before `unionByName()`.

---

# OMOP Mapping Issues

## Person Mapping Failed

### Solution

Verify that:

- Silver Patient table contains valid records.
- Required fields are populated.
- Patient identifiers are unique.

---

## Visit Mapping Failed

### Solution

Ensure that:

- Every encounter references a valid patient.
- Encounter dates are available.

---

# Gold Layer Issues

## KPI Tables Empty

### Solution

Verify that:

- OMOP tables contain data.
- Previous pipeline stages completed successfully.

---

## Incorrect Readmission Rate

### Solution

Verify that:

- Encounter dates are sorted correctly.
- Readmission calculation uses a 30-day window.

---

## Patient Cohorts Empty

### Solution

Check that:

- Patient demographic data exists.
- Cohort logic is correctly applied.

---

# Machine Learning Pipeline Issues

## XGBoost Module Not Found

Error

```
ModuleNotFoundError: No module named 'xgboost'
```

### Solution

Run the Bootstrap notebook to install the required Python packages before executing the ML pipeline.

---

## Training Dataset Empty

### Solution

Verify that:

- Gold tables contain data.
- Dataset joins were successful.
- Required features are available.

---

## MLflow Registration Failed

### Solution

Verify that:

- MLflow Tracking is enabled.
- Model Registry permissions are available.
- The experiment path is valid.

---

## Champion Alias Not Found

### Solution

Verify that:

- A model has been registered.
- The Champion alias has been assigned.

---

# Model Inference Issues

## No New Patients Available

Message

```
No new patient data available for inference.
```

### Explanation

No new records were found for prediction.

This is expected behavior.

---

## Prediction Failed

### Solution

Verify that:

- The Champion model exists.
- Feature columns match the training dataset.
- Required Gold tables have been refreshed.

---

# Workflow Issues

## Workflow Failed

### Solution

Verify that:

- Cluster is running.
- Notebook paths are correct.
- Required permissions are available.

---

## Bootstrap Notebook Failed

### Solution

Verify that:

- Internet access is available for package installation.
- Required packages can be downloaded from PyPI.

---

## Notebook Dependency Failed

### Solution

Ensure that:

- Previous notebook completed successfully.
- `%run ./00_Utilities` executes without errors.

---

# Dashboard Issues

## Dashboard Shows No Data

### Solution

Verify that:

- Gold tables contain data.
- Prediction table contains records.
- SQL Warehouse is running.

---

## Dashboard Not Refreshing

### Solution

Ensure that:

- Daily Refresh workflow completed successfully.
- Dashboard queries reference the correct catalog and schema.

---

## Dashboard Import Failed

### Solution

Verify that:

- The `.lvdash.json` file is valid.
- Dashboard permissions are available.
- SQL Warehouse exists in the target workspace.

---

# Performance Issues

## Pipeline Running Slowly

### Possible Causes

- Large datasets
- Small cluster size
- Inefficient joins

### Solution

- Use Delta tables.
- Optimize joins.
- Increase cluster resources if required.
- Use partitioning where appropriate.

---

# Common Verification Checklist

Before running the project, verify that:

- Azure resources are created.
- Secret Scope is configured.
- Unity Catalog is configured.
- Bootstrap notebook completed successfully.
- Storage paths are correct.
- Raw healthcare files are available.
- SQL Warehouse is running.
- Workflows are imported.
- Dashboard has been imported.
- Champion model exists in MLflow.

---

# Getting Additional Help

If the issue persists:

1. Review the Databricks notebook logs.
2. Check the Workflow execution logs.
3. Verify Azure permissions and storage access.
4. Confirm that all previous pipeline stages completed successfully.

Following the pipeline in execution order (Bronze → Silver → OMOP → Gold → ML → Inference → Dashboard) usually helps identify the root cause quickly.