# Workflows

Databricks Workflows automate the execution of the Healthcare Lakehouse pipeline.

Instead of manually running notebooks one by one, workflows orchestrate the complete data pipeline in the correct execution order.

This project contains two workflows:

- Initial Setup Workflow
- Daily Refresh Workflow

---

# Why Workflows?

Using Databricks Workflows provides:

- End-to-end automation
- Scheduled execution
- Dependency management
- Failure monitoring
- Retry capability
- Production-ready orchestration

---

# Workflow Architecture

```
                Initial Setup Workflow
                        │
                        ▼
            Install Python Libraries
                        │
                        ▼
             Unity Catalog Setup
                        │
                        ▼
              Bronze Layer
                        │
                        ▼
               Silver Layer
                        │
                        ▼
               OMOP Mapping
                        │
                        ▼
                 Gold Layer
                        │
                        ▼
         Machine Learning Pipeline
                        │
                        ▼
            Register Champion Model
```

```
                 Daily Refresh Workflow
                        │
                        ▼
              Bronze Layer
                        │
                        ▼
               Silver Layer
                        │
                        ▼
               OMOP Mapping
                        │
                        ▼
                 Gold Layer
                        │
                        ▼
              Model Inference
                        │
                        ▼
              Dashboard Refresh
```

---

# Workflow 1 — Initial Setup

The Initial Setup workflow is executed only once when deploying the project.

Its purpose is to configure the environment and train the initial machine learning model.

## Tasks

1. Install required Python libraries
2. Configure Unity Catalog
3. Execute Bronze Layer
4. Execute Silver Layer
5. Execute OMOP Mapping
6. Execute Gold Layer
7. Execute Machine Learning Pipeline
8. Register model in MLflow
9. Assign Champion alias

---

# Initial Setup Execution Flow

```
Bootstrap

↓

Unity Catalog

↓

Bronze

↓

Silver

↓

OMOP

↓

Gold

↓

ML Training

↓

Champion Model
```

---

# Workflow 2 — Daily Refresh

The Daily Refresh workflow executes continuously in production.

Unlike the Initial Setup workflow, it does **not** retrain the machine learning model.

Instead, it generates predictions using the already registered Champion model.

## Tasks

1. Bronze Layer
2. Silver Layer
3. OMOP Mapping
4. Gold Layer
5. Model Inference

---

# Daily Workflow Execution Flow

```
New Healthcare Data

↓

Bronze

↓

Silver

↓

OMOP

↓

Gold

↓

Load Champion Model

↓

Predict Readmission Risk

↓

Store Predictions
```

---

# Why Training Is Not Included in Daily Workflow

Model training is computationally expensive and only needs to occur when a new model is required.

The daily workflow:

- Uses the existing Champion model
- Scores only new patient records
- Produces predictions quickly
- Avoids unnecessary retraining

This follows standard MLOps best practices.

---

# Workflow Dependencies

Each notebook starts only after the previous notebook completes successfully.

Execution order

```
Bootstrap

↓

Bronze

↓

Silver

↓

OMOP

↓

Gold

↓

ML / Inference
```

If any notebook fails, downstream notebooks are not executed.

---

# Scheduling

The Initial Setup workflow is executed manually during project deployment.

The Daily Refresh workflow can be scheduled to run:

- Every hour
- Every day
- Every week
- On-demand

depending on business requirements.

---

# Monitoring

Databricks Workflows provides execution monitoring including:

- Start Time
- End Time
- Execution Duration
- Success / Failure Status
- Task Logs
- Error Messages

This simplifies production monitoring and debugging.

---

# Workflow JSON

The project includes exported Databricks Workflow JSON files.

```
workflows/

├── Healthcare_Lakehouse_Pipeline_Initial_Setup.json
├── Healthcare_Lakehouse_Pipeline_Daily_Refresh.json
```

These files can be imported into another Databricks workspace to recreate the workflows without manual configuration.

---

# Importing Workflows

To import a workflow:

1. Open **Databricks Workspace**.
2. Navigate to **Workflows**.
3. Click **Import Workflow**.
4. Select the corresponding JSON file.
5. Update notebook paths if required.
6. Save the workflow.
7. Run the workflow.

---

# Best Practices

This project follows several workflow best practices:

- Separate deployment and production workflows
- Execute notebooks sequentially
- Avoid duplicate processing
- Train models only when required
- Use the Champion model for production inference
- Log execution status through the Pipeline Audit table

---

# Troubleshooting

## Workflow Failed

Verify:

- Cluster is running
- Required permissions are available
- Notebook paths are correct

---

## Notebook Not Found

Ensure that notebook paths in the imported workflow match the project structure.

---

## Model Not Found During Inference

Verify that:

- Initial Setup workflow has completed successfully.
- The Champion model exists in the MLflow Model Registry.

---

## Pipeline Stops Midway

Check the failed task logs in Databricks Workflows and rerun the failed notebook after resolving the issue.

---

# Next Step

After the workflows are configured, the Gold tables and prediction outputs can be visualized using the Healthcare Analytics Dashboard in Databricks SQL and Power BI.