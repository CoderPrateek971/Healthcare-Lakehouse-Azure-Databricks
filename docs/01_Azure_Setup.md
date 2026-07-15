# Azure Setup Guide

This guide explains how to provision all Azure resources required to deploy the Healthcare Lakehouse project.

By the end of this guide, you will have:

- Azure Resource Group
- Azure Data Lake Storage Gen2
- Azure Databricks Workspace
- Unity Catalog
- Storage Credential
- External Location
- Catalog & Schemas
- Secret Scope
- Databricks Compute

---

# Prerequisites

Before starting, ensure you have:

- Azure Subscription
- Contributor access to the subscription
- Azure Databricks Workspace permissions
- Azure CLI (optional)
- Git installed

---

# Architecture

Azure Resources

↓

Azure Resource Group

↓

Azure Data Lake Storage Gen2

↓

Azure Databricks

↓

Unity Catalog

↓

Lakehouse

---

# Step 1 — Create Resource Group

Azure Portal

↓

Resource Groups

↓

Create

Configure:

Resource Group Name

```
Healthcare-RG
```

Region

```
Your region
```

Click

```
Create
```

---

# Step 2 — Create Storage Account

Azure Portal

↓

Storage Accounts

↓

Create

Configuration

Storage Account Name

```
healthcarelakehouse
```

Performance

```
Standard
```

Redundancy

```
LRS
```

Enable

```
Hierarchical Namespace

✔ Enabled
```

This converts the storage account into **Azure Data Lake Storage Gen2**.

---

# Step 3 — Create Storage Containers

Inside Storage Account

Create the following containers

```
raw

bronze

```

Final structure

```
Storage Account

├── raw

├── bronze

```

---

# Step 4 — Upload Sample Dataset

Upload the datasets into the Raw container.

Example

```
raw/

    fhir/

    hl7/

    flat files/
```

The repository includes sample healthcare datasets generated using **Synthea**.

---

# Step 5 — Create Azure Databricks Workspace

Azure Portal

↓

Azure Databricks

↓

Create

Workspace Name

```
Healthcare-Databricks
```

Pricing Tier

```
Premium
```

After deployment

Launch Workspace.

---

# Step 6 — Create Access Connector

Azure Portal

↓

Access Connector for Azure Databricks

↓

Create

Assign

```
Storage Blob Data Contributor
```

permission on the Storage Account.

---

# Step 7 — Create Azure Key Vault

Azure Portal

↓

Key Vault

↓

Create

### Create Databricks Secrets

Create the following secrets inside your Databricks Secret Scope.

| Secret Name | Example Value |
|-------------|---------------|
| raw_root | `abfss://raw@<storage-account>.dfs.core.windows.net/` |
| bronze_root | `abfss://bronze@<storage-account>.dfs.core.windows.net/` |
| silver_root | `abfss://silver@<storage-account>.dfs.core.windows.net/` |
| gold_root | `abfss://gold@<storage-account>.dfs.core.windows.net/` |
| omop_root | `abfss://gold@<storage-account>.dfs.core.windows.net/omop/` |

These values are read by the notebooks at runtime to ensure the project remains portable across Azure environments.

---

# Step 8 — Create Databricks Secret Scope

Inside Databricks


```
Workspace
    ↓
Catalog
    ↓
Secrets
    ↓
Create Secret Scope
```

Secret Scope Name

```
healthcare-scope
```

Click **Create**.


---

# Step 9 — Enable Unity Catalog

Inside Databricks

Create

```
Metastore
```

Assign it to your workspace.

---

# Step 10 — Create Storage Credential

Inside Catalog Explorer

Create

```
Storage Credential
```

using the Access Connector created earlier.

---

# Step 11 — Create External Location

Create an External Location pointing to

```
abfss://raw@<storage-account>.dfs.core.windows.net/
```

Grant

```
Read Files

Write Files
```

permissions.

---

# Step 12 — Create Catalog

Create Catalog

Example

```
db_healthcare_kl
```

---

# Step 13 — Create Schemas

The Healthcare Lakehouse project uses the following Unity Catalog schemas:

```
bronze
silver
gold
omop
```

These schemas are **created automatically** during execution of the **Healthcare_Lakehouse_Pipeline_Initial_Setup** workflow.

No manual schema creation is required.

After the Initial Setup workflow completes, verify that the following schemas exist under your catalog:

```
db_healthcare_kl

├── bronze
├── silver
├── gold
└── omop
```

# Step 14 — Create Compute

Create a Databricks Cluster.

Recommended

```
Runtime

15.x LTS

Access Mode

Standard

Autoscaling

Optional
```

Attach all notebooks to this cluster.

---

# Step 15 — Clone Repository

Clone this repository

```bash
git clone https://github.com/<username>/Healthcare-Lakehouse.git
```

---

# Step 16 — Import Notebooks

Inside Databricks

Workspace

↓

Import

↓

Select all notebooks from

```
notebooks/
```

---

# Step 17 — Import Workflows

Import

```
Healthcare_Lakehouse_Pipeline_Initial_Setup.json

Healthcare_Lakehouse_Pipeline_Daily_Run.json

Silver_DLT_Pipeline.json
```

from

```
workflows/
```

Refer to

```
docs/08_Workflows.md
```

for detailed instructions.

---

# Step 18 — Import Dashboard

Import

```
Healthcare_Dashboard.lvdash.json
```

from

```
dashboards/
```

Refer to

```
docs/09_Dashboard.md
```

for detailed instructions.

---

# Final Setup Checklist

✔ Azure Resource Group

✔ ADLS Gen2 Storage Account

✔ Containers Created

✔ Dataset Uploaded

✔ Azure Databricks Workspace

✔ Unity Catalog Enabled

✔ Storage Credential Created

✔ External Location Created

✔ Catalog Created

✔ Schemas Created

✔ Cluster Running

✔ Notebooks Imported

✔ Workflows Imported

✔ Dashboard Imported

You are now ready to execute the Healthcare Lakehouse pipeline.