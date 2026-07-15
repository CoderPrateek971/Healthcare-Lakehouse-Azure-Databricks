# Model Inference

The Model Inference notebook is responsible for generating predictions for new patient encounters using the latest production-ready Machine Learning model.

Unlike the Machine Learning Pipeline, this notebook does not perform model training or evaluation. Instead, it loads the registered **Champion** model from the MLflow Model Registry and predicts the risk of 30-day hospital readmission.

---

# Purpose

The Model Inference notebook is responsible for:

- Loading the production model
- Reading new patient data
- Preparing inference features
- Generating predictions
- Saving prediction results
- Supporting continuous batch inference

---

# Why a Separate Inference Pipeline?

Separating model training from inference provides several advantages.

- Faster daily execution
- No unnecessary model retraining
- Consistent production predictions
- Independent deployment of new models
- Easier maintenance and monitoring

---

# Inference Architecture

```
New Patient Data

        │

        ▼

Gold Layer

        │

        ▼

Feature Preparation

        │

        ▼

Load Champion Model

        │

        ▼

Prediction

        │

        ▼

Prediction Table

        │

        ▼

Dashboard / Reporting
```

---

# Input Data

The notebook reads the following datasets.

| Table | Description |
|--------|-------------|
| gold.encounter_summaries | Patient encounter summary |
| gold.patient_cohorts | Patient cohort information |
| gold.readmission_within_30days | Readmission reference data |
| omop.person_data | Patient demographic information |
| omop.condition_occurrence | Diagnosis history |
| omop.drug_exposure | Medication history |
| omop.visit_occurrence | Hospital encounters |

---

# Model Loading

The notebook loads the latest production model from the MLflow Model Registry.

Instead of loading a specific model version, the notebook loads the model using the **Champion** alias.

Example

```
MLflow Model Registry

        │

        ▼

Champion Model

        │

        ▼

Load Model
```

This ensures that the inference pipeline always uses the latest approved production model.

---

# Feature Preparation

The notebook prepares inference features using the same preprocessing logic that was applied during model training.

Typical features include:

- Patient Age
- Gender
- Length of Stay
- Previous Admissions
- Diagnosis History
- Medication History

Using identical feature engineering guarantees consistency between training and inference.

---

# Prediction

The loaded model predicts whether a patient is likely to be readmitted within 30 days.

Prediction Flow

```
Patient Features

        │

        ▼

Champion Model

        │

        ▼

Prediction

        │

        ▼

Readmission Risk
```

---

# Prediction Output

Each prediction includes:

- Patient Identifier
- Prediction Label
- Prediction Probability
- Prediction Timestamp

The results are stored for downstream analytics and reporting.

---

# Continuous Batch Inference

The project follows a production-style batch inference workflow.

```
Raw Files Arrive

↓

Bronze

↓

Silver

↓

OMOP

↓

Gold

↓

Model Inference

↓

Prediction Table
```

Only newly arrived patient records are scored during each pipeline execution, preventing duplicate predictions.

---

# Pipeline Integration

The inference notebook is executed as part of the **Healthcare Lakehouse Daily Refresh** workflow.

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

Model Inference

↓

Dashboard Refresh
```

The Machine Learning training pipeline is **not** executed during the daily workflow.

---

# Output

The notebook produces:

- Readmission predictions
- Prediction probabilities
- Production-ready prediction dataset

These outputs are available for dashboards and downstream reporting.

---

# Benefits

The Model Inference notebook provides:

- Fast production predictions
- Reusable trained model
- Automated batch scoring
- Consistent inference pipeline
- Scalable production workflow

---

# Troubleshooting

## Champion Model Not Found

Verify that:

- A model has been registered in MLflow.
- The Champion alias has been assigned.

---

## No New Patients Available

If no new patient records are available, the notebook exits without generating predictions.

---

## Feature Mismatch

Ensure that the inference dataset uses the same schema and feature engineering as the training dataset.

---

## Prediction Failure

Verify that:

- Required Gold tables exist.
- MLflow Model Registry is accessible.
- The Champion model is available.

---

# Next Step

The generated predictions are consumed by healthcare dashboards and reporting systems, enabling clinicians and administrators to identify patients at high risk of 30-day hospital readmission.