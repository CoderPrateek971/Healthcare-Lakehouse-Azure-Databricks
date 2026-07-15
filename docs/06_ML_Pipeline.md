# Machine Learning Pipeline

The Machine Learning Pipeline is responsible for training, evaluating, and registering the healthcare readmission prediction model.

Unlike the Gold layer, this notebook focuses entirely on machine learning by preparing the training dataset, building the ML pipeline, training the model, evaluating its performance, and registering the best model in MLflow.

Model inference is performed separately in the **Model Inference** notebook.

---

# Purpose

The Machine Learning Pipeline is responsible for:

- Reading analytics-ready datasets
- Preparing the training dataset
- Feature engineering
- Training the XGBoost model
- Evaluating model performance
- Logging experiments to MLflow
- Registering the trained model
- Assigning the Champion model alias

---

# Why a Separate ML Pipeline?

Separating model training from inference provides several benefits.

- Model training is performed only when required.
- Daily inference remains lightweight.
- Models can be retrained without affecting production predictions.
- Supports model versioning and experiment tracking.

---

# ML Pipeline Architecture

```
Gold Tables

        │

        ▼

OMOP Tables

        │

        ▼

Feature Engineering

        │

        ▼

Train / Test Split

        │

        ▼

Spark ML Pipeline

        │

        ▼

XGBoost Training

        │

        ▼

Model Evaluation

        │

        ▼

MLflow Tracking

        │

        ▼

Model Registry

        │

        ▼

Champion Model
```

---

# Input Tables

The notebook reads the following datasets.

## Gold Layer

| Table | Description |
|--------|-------------|
| gold.encounter_summaries | Patient encounter summary |
| gold.patient_cohorts | Patient cohort information |
| gold.readmission_within_30days | Readmission labels |

## OMOP Layer

| Table | Description |
|--------|-------------|
| omop.person_data | Patient demographics |
| omop.condition_occurrence | Diagnoses |
| omop.drug_exposure | Medication history |
| omop.visit_occurrence | Hospital encounters |

---

# Dataset Preparation

The notebook joins Gold and OMOP datasets to create a single machine learning dataset.

Example

```
Gold Tables

        +

OMOP Tables

        │

        ▼

ML Dataset
```

Each row represents a patient encounter with all features required for prediction.

---

# Feature Engineering

The notebook prepares machine learning features by performing:

- Selecting relevant columns
- Handling missing values
- Encoding categorical variables
- Creating feature vectors

Typical features include:

- Patient Age
- Gender
- Length of Stay
- Previous Admissions
- Diagnosis Information
- Medication History

---

# Data Preprocessing

The Spark ML Pipeline is used to prepare the dataset before model training.

Pipeline stages include:

- StringIndexer
- OneHotEncoder
- VectorAssembler

This converts raw healthcare attributes into numerical feature vectors suitable for machine learning.

---

# Train-Test Split

The prepared dataset is divided into:

```
Training Dataset

80%

↓

Model Training

------------------------

Testing Dataset

20%

↓

Model Evaluation
```

The testing dataset is never used during model training.

---

# Model Training

The project uses the **XGBoost Classifier** to predict 30-day hospital readmissions.

XGBoost was selected because it:

- Handles structured healthcare data efficiently
- Performs well on tabular datasets
- Supports classification problems
- Provides high predictive performance

---

# Model Evaluation

After training, the model is evaluated using standard classification metrics.

Metrics include:

- Accuracy
- Precision
- Recall
- F1 Score
- ROC-AUC Score

These metrics help compare different model versions.

---

# MLflow Experiment Tracking

Each training run is logged to MLflow.

Logged artifacts include:

- Hyperparameters
- Evaluation metrics
- Feature importance
- Trained model
- Execution metadata

This provides complete experiment tracking.

---

# Model Registration

After successful evaluation, the trained model is registered in the MLflow Model Registry.

Example

```
MLflow Run

        │

        ▼

Register Model

        │

        ▼

Healthcare_Readmission_Model
```

Each trained model receives a unique version.

---

# Champion Model

The best-performing model is assigned the **Champion** alias.

```
Version 1

↓

Champion
```

The inference pipeline always loads the Champion model instead of referencing a specific version number.

This allows new models to be promoted without modifying inference code.

---

# Pipeline Integration

The Machine Learning Pipeline executes after the Gold Layer.

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

Machine Learning Training

↓

Model Registry
```

The pipeline is executed only when model retraining is required.

---

# Output

The notebook produces:

- Trained XGBoost Model
- MLflow Experiment
- Registered Model
- Champion Model Alias

These outputs are consumed by the Model Inference notebook.

---

# Benefits

The Machine Learning Pipeline provides:

- Automated model training
- Experiment tracking
- Model versioning
- Reproducible training
- Production-ready model registry

---

# Troubleshooting

## Model Training Failed

Verify that the machine learning dataset contains valid records and required features.

---

## Missing Features

Ensure that Gold and OMOP tables have been generated successfully before executing the notebook.

---

## MLflow Registration Failed

Verify that:

- MLflow Tracking Server is accessible.
- Model Registry permissions are configured correctly.

---

## Champion Alias Not Found

Ensure that a model has been registered before assigning the Champion alias.

---

# Next Step

The registered **Champion** model is loaded by the **Model Inference** notebook to generate predictions for new patient encounters without retraining the model.