# Section 1: Databricks Intelligence Platform - Learning Guide

Exam guide version: NEW EXAM GUIDE, valid on or after 4-May-2026.

Official Databricks documentation often uses the product name "Databricks Data Intelligence Platform." The exam outline title says "Databricks Intelligence Platform." Treat these as the same platform context for this section.

## Section Objectives

This section covers two syllabus bullets from the NEW EXAM GUIDE:

1. Understand the core components of the Databricks Data Intelligence Platform, such as its architecture, Delta Lake, and Unity Catalog.
2. Understand Databricks Data Intelligence Platform compute services, including their characteristics, limitations, and cost models, and select the most suitable option for each workload use case.

## Exam Mental Model

Databricks is not only "Spark in the cloud." For the Associate exam, think of it as a governed lakehouse platform with four cooperating layers:

- Workspace and account layer: users, groups, workspaces, notebooks, jobs, SQL assets, repos/Git folders, policies, and billing.
- Architecture layer: control plane and compute plane.
- Storage and table layer: cloud object storage, Delta Lake tables, transaction logs, managed/external storage locations, and Unity Catalog names.
- Compute layer: serverless compute, classic all-purpose compute, classic job compute, SQL warehouses, and pipeline compute.

When a question asks "which Databricks capability should be used," first identify whether the problem is about governance, storage reliability, interactive development, scheduled production execution, SQL analytics, or low-level compute customization.

## Core Architecture

### Account

A Databricks account is the top-level administrative boundary. Account-level responsibilities commonly include identity management, workspace management, Unity Catalog metastore management, usage, billing, and account-wide policies.

Exam focus:

- Account admins manage account-level resources.
- One account can contain multiple workspaces.
- Unity Catalog metastores can be attached to workspaces, subject to region and cloud constraints.

### Workspace

A workspace is the collaborative environment where users create and run Databricks assets. Typical workspace assets include notebooks, queries, dashboards, jobs, Git folders, libraries, and configuration files.

Exam focus:

- Data engineers use workspaces to develop notebooks, schedule jobs, and run pipelines.
- Workspaces are not the same as Unity Catalog metastores.
- A workspace can be attached to a Unity Catalog metastore so users can reference governed data with a three-level namespace.

### Control Plane and Compute Plane

Databricks architecture separates control services from data processing.

- Control plane: Databricks-managed backend services such as the web application, workspace metadata, job orchestration metadata, and other platform services.
- Compute plane: where code actually processes data.

There are two broad compute plane patterns:

- Classic compute plane: compute resources run in the customer's cloud account or cloud subscription.
- Serverless compute plane: Databricks manages the compute resources in Databricks-managed infrastructure in the same cloud region pattern as the workspace.

Exam focus:

- The control plane is managed by Databricks.
- The compute plane is where data processing happens.
- Classic compute gives more infrastructure control.
- Serverless compute reduces infrastructure management.

## Delta Lake

Delta Lake is the optimized storage layer that provides the foundation for lakehouse tables in Databricks. It stores table data as Parquet files plus a transaction log that tracks table versions, schema, metadata, and committed operations.

### Why Delta Lake Matters

Delta Lake gives data engineering workloads database-like reliability on cloud object storage:

- ACID transactions: readers and writers see consistent table state.
- Scalable metadata handling: the transaction log records table metadata and operations.
- Schema enforcement: invalid writes can be rejected when they do not match the table schema.
- Schema evolution: controlled schema changes can be allowed when needed.
- Time travel: previous table versions can be queried for debugging, recovery, and reproducibility.
- Unified batch and streaming: the same table can support batch and streaming workloads.

Exam focus:

- Delta is the default table format on Databricks unless another supported format is specified.
- Delta Lake is the table reliability layer; Unity Catalog is the governance layer.
- Delta Lake is not itself a compute service.

### Basic Delta Table SQL

Create a governed Delta table in Unity Catalog:

```sql
CREATE CATALOG IF NOT EXISTS de_academy;
CREATE SCHEMA IF NOT EXISTS de_academy.bronze;

CREATE OR REPLACE TABLE de_academy.bronze.orders_raw (
  order_id STRING,
  customer_id STRING,
  order_ts TIMESTAMP,
  amount DECIMAL(10, 2)
)
USING DELTA;
```

Insert and query data:

```sql
INSERT INTO de_academy.bronze.orders_raw
VALUES
  ('o1001', 'c001', TIMESTAMP '2026-05-01 10:15:00', 250.00),
  ('o1002', 'c002', TIMESTAMP '2026-05-01 10:20:00', 125.50);

SELECT order_id, amount
FROM de_academy.bronze.orders_raw
WHERE amount > 200;
```

Review table metadata and history:

```sql
DESCRIBE DETAIL de_academy.bronze.orders_raw;

DESCRIBE HISTORY de_academy.bronze.orders_raw;
```

Query an earlier table version:

```sql
SELECT *
FROM de_academy.bronze.orders_raw VERSION AS OF 0;
```

### Basic Delta with PySpark

Write a DataFrame to a Unity Catalog table:

```python
orders_df.write \
    .format("delta") \
    .mode("append") \
    .saveAsTable("de_academy.bronze.orders_raw")
```

Read the table back:

```python
orders = spark.table("de_academy.bronze.orders_raw")
display(orders)
```

### Optimization Concepts to Recognize

Although detailed performance tuning appears later in the exam, Section 1 questions can still test the platform role of Delta optimizations.

- Data skipping: Databricks can skip irrelevant files using stored file statistics.
- OPTIMIZE: compacts smaller files into larger files for better scan performance.
- Z-ordering: older layout technique for colocating related values; still may appear in legacy scenarios.
- Liquid clustering: newer layout approach that lets Databricks manage clustering more flexibly than static partitioning strategies.
- Predictive optimization: Databricks-managed optimization that can automate selected maintenance operations where supported.

Example maintenance command:

```sql
OPTIMIZE de_academy.bronze.orders_raw;
```

Exam focus:

- Do not choose manual file edits in Delta directories. Databricks recommends using Delta APIs and SQL commands.
- Do not treat partitioning as the only layout tool. Databricks has managed layout and optimization features.

## Unity Catalog

Unity Catalog is the unified governance layer for data and AI assets in Databricks. It provides centralized access control, data discovery, lineage, auditability, and data sharing capabilities across workspaces.

### Object Model

Unity Catalog organizes governed assets using a hierarchy.

```text
metastore
  catalog
    schema
      table
      view
      volume
      function
      model
```

Most table references use the three-level namespace:

```text
catalog.schema.object
```

Example:

```sql
SELECT *
FROM de_academy.bronze.orders_raw;
```

Exam focus:

- Catalogs are top-level data organization boundaries.
- Schemas live inside catalogs.
- Tables, views, functions, and volumes live inside schemas.
- Metastores are attached to workspaces and can provide a consistent data view across multiple workspaces in the same supported region context.

### Unity Catalog and Delta Lake Together

Unity Catalog governs access to tables. Delta Lake provides the table storage and transaction layer.

Common exam distinction:

- "Who can read this table?" points to Unity Catalog privileges.
- "How does the table support ACID writes and time travel?" points to Delta Lake.
- "How do I reference the table consistently across workspaces?" points to Unity Catalog metastore and three-level naming.

### Basic Governance SQL

Create a catalog and schema:

```sql
CREATE CATALOG IF NOT EXISTS de_academy;
CREATE SCHEMA IF NOT EXISTS de_academy.silver;
```

Grant access:

```sql
GRANT USE CATALOG ON CATALOG de_academy TO `data-engineers`;
GRANT USE SCHEMA ON SCHEMA de_academy.silver TO `data-engineers`;
GRANT SELECT ON TABLE de_academy.silver.customer_orders TO `analysts`;
```

Revoke access:

```sql
REVOKE SELECT ON TABLE de_academy.silver.customer_orders FROM `analysts`;
```

Exam focus:

- Users need enough privilege at each level of the hierarchy.
- A table-level `SELECT` grant alone is not enough if the principal cannot use the containing catalog and schema.
- Deep privilege details are in the Governance and Security section, but Section 1 can test the purpose of Unity Catalog.

### Managed and External Storage Awareness

You should recognize the difference at a high level:

- Managed tables: Unity Catalog manages the table and underlying storage location lifecycle.
- External tables: Unity Catalog governs Databricks access, but the underlying storage path is managed outside Databricks.
- External locations and storage credentials are securable objects used to govern cloud storage access.
- Volumes provide governed access to non-tabular files.

Detailed operations on managed and external tables are in the Governance and Security section.

## Compute Services

Compute is the set of processing resources used to run notebooks, jobs, pipelines, SQL queries, dashboards, and data engineering workloads.

### Serverless Compute

Serverless compute is Databricks-managed compute for notebooks, Lakeflow Jobs, and Lakeflow Spark Declarative Pipelines. It is designed to reduce setup work, start quickly, scale automatically, and minimize idle infrastructure management.

Best fit:

- Interactive Python or SQL notebooks when serverless is supported.
- Most automated jobs that do not require custom cluster settings.
- Lakeflow Jobs tasks such as notebooks, Python scripts, and Python wheels where serverless is supported.
- Serverless Lakeflow Spark Declarative Pipelines.

Key limitations to recognize:

- R is not supported on serverless notebooks/jobs.
- Spark RDD APIs are not supported because serverless uses Spark Connect APIs.
- Some Spark configurations and compute-scoped customizations are unavailable.
- Direct DBFS and DBFS mount patterns are limited; use Unity Catalog volumes, external locations, or workspace files where appropriate.
- Spark UI is not available for serverless notebooks/jobs; use query profile/query insights instead.
- Some streaming triggers are unsupported; `AvailableNow` is the preferred trigger pattern for many serverless streaming cases.

Exam selection rule:

Choose serverless when the workload is standard, supported, and benefits from fast startup and Databricks-managed scaling. Do not choose it when the scenario requires unsupported languages, RDD APIs, compute-scoped init scripts, custom Spark extensions, or deep infrastructure customization.

### Classic All-Purpose Compute

Classic all-purpose compute is user-managed or policy-managed compute for interactive work.

Best fit:

- Interactive notebook exploration where serverless is unavailable or unsupported.
- Workloads requiring R, RDD APIs, custom cluster settings, custom libraries, or lower-level Spark behavior.
- Development scenarios that need long-lived interactive compute.

Exam selection rule:

All-purpose compute is appropriate for interactive development, but generally avoid it for production jobs because it is not optimized for automated workloads and can remain idle.

### Classic Jobs Compute

Classic jobs compute is provisioned for automated job execution.

Best fit:

- Non-SQL production jobs requiring cluster settings not available in serverless.
- JAR or Spark submit workloads.
- Jobs that need custom libraries, specific runtimes, init scripts, GPU instances, or networking settings.

Exam selection rule:

For production jobs, prefer serverless jobs compute when supported. Use classic jobs compute when serverless cannot meet the workload requirements.

### SQL Warehouses

SQL warehouses are optimized compute resources for SQL queries, BI dashboards, analytics, and SQL-based ETL.

Types to recognize:

- Serverless SQL warehouse: recommended for BI, ETL, and exploratory SQL where available; starts quickly and scales rapidly.
- Pro SQL warehouse: useful when serverless is unavailable or custom networking is required.
- Classic SQL warehouse: basic interactive SQL option when serverless or pro is not available.

Exam selection rule:

If the task is a SQL query, dashboard, BI tool connection, or dbt SQL workload, choose a SQL warehouse instead of a general-purpose cluster unless the scenario gives a special reason.

### Lakeflow Pipeline Compute

Lakeflow Spark Declarative Pipelines can run on serverless pipeline compute or classic pipeline compute.

Best fit:

- Managed ETL pipelines where Databricks handles dependencies and pipeline execution.
- Use serverless pipeline compute for most supported workloads.
- Use classic pipeline compute when the scenario needs a feature not supported by serverless or references a legacy Hive metastore requirement.

### Photon

Photon is Databricks' native vectorized query engine. It accelerates SQL workloads, DataFrame operations, ETL pipelines, and supported stateless streaming workloads without requiring code changes.

Exam focus:

- Photon is enabled automatically for serverless compute, SQL warehouses, and serverless Lakeflow Spark Declarative Pipelines.
- Photon improves many scan, join, aggregate, and write workloads.
- Photon can fall back to Spark execution for unsupported operations.
- Photon does not support RDD APIs, Dataset APIs, or UDF-heavy patterns in the same way as standard Spark execution.
- Photon-enabled instance types can consume DBUs differently from non-Photon equivalents.

## Cost Model Awareness

The exam usually tests cost model conceptually, not exact prices.

### Cost Concepts

- DBU: Databricks Unit, a normalized unit used for Databricks usage measurement and pricing.
- Classic compute: often includes Databricks DBU charges plus cloud provider infrastructure charges for VMs, disks, and networking.
- Serverless compute: Databricks manages infrastructure and bills through serverless SKUs/DBUs; users trade lower management overhead for less infrastructure control.
- SQL warehouses: billed based on warehouse type, size, runtime, and scaling behavior.
- Idle time matters: long-running all-purpose clusters can waste cost when left running.
- Autoscaling and auto-termination can reduce waste.
- Instance pools can reduce startup time and may improve usage efficiency for classic compute.

### Cost-Based Compute Choices

| Scenario | Cost-aware choice | Why |
| --- | --- | --- |
| Ad hoc SQL dashboard used by analysts | Serverless SQL warehouse | Fast startup, optimized SQL execution, no cluster management |
| Scheduled notebook ETL with standard Spark APIs | Serverless jobs compute | Databricks manages scaling and infrastructure |
| Production job needing init scripts and custom Spark configs | Classic jobs compute | Serverless may not support required customization |
| Interactive notebook with R | Classic all-purpose compute | R is not supported in serverless notebooks/jobs |
| Notebook that uses RDD APIs | Classic all-purpose or classic jobs compute | Serverless does not support Spark RDD APIs |
| SQL task in Lakeflow Jobs | SQL warehouse | SQL workloads should run on SQL-optimized compute |
| Long-running collaborative exploration | Standard all-purpose compute with auto-termination | Shared interactive compute with governance and idle-cost controls |

## Compute Selection Decision Tree

Use this exam shortcut:

1. Is the workload SQL analytics, dashboards, BI, or SQL-only dbt?
   - Choose a SQL warehouse.
2. Is it a supported automated job or pipeline with standard Python/SQL/DataFrame logic?
   - Choose serverless jobs or serverless pipeline compute.
3. Does it require R, RDD APIs, init scripts, compute-scoped libraries, custom Spark extensions, custom networking, GPU, or unsupported triggers?
   - Choose classic compute, usually classic jobs compute for production or all-purpose compute for interactive development.
4. Is the work interactive exploration in a notebook?
   - Choose serverless notebooks if supported; otherwise classic all-purpose compute.
5. Is the question asking about fast startup and minimal infrastructure management?
   - Serverless is usually the best answer.
6. Is the question asking about maximum customization or unsupported serverless features?
   - Classic compute is usually the best answer.

## Practical CLI Examples

List available classic clusters:

```bash
databricks clusters list
```

List SQL warehouses:

```bash
databricks warehouses list
```

Get job details to inspect task compute:

```bash
databricks jobs get 123456789
```

Exam focus:

- CLI commands are usually less important than knowing which compute class fits the workload.
- For CI/CD, Declarative Automation Bundles and CLI deployment are tested later.

## Common Exam Traps

- Confusing Delta Lake with Unity Catalog: Delta handles table reliability; Unity Catalog handles governance and discovery.
- Choosing all-purpose compute for production jobs: jobs compute or serverless jobs compute is usually preferred.
- Choosing serverless when the question says R, RDD, init scripts, compute-scoped libraries, or unsupported custom Spark configs.
- Choosing a cluster for BI dashboards when a SQL warehouse is the intended compute.
- Forgetting the three-level Unity Catalog namespace: `catalog.schema.object`.
- Assuming DBFS mounts are the modern governance pattern. Unity Catalog external locations and volumes are the preferred governed pattern.
- Treating the control plane as where data transformations execute. Data processing happens in the compute plane.
- Assuming Photon requires code changes. It accelerates supported operations transparently.

## Mini Labs

### Lab 1: Create and Query a Delta Table

```sql
CREATE CATALOG IF NOT EXISTS de_academy;
CREATE SCHEMA IF NOT EXISTS de_academy.bronze;

CREATE OR REPLACE TABLE de_academy.bronze.web_events (
  event_id STRING,
  event_ts TIMESTAMP,
  user_id STRING,
  event_type STRING
)
USING DELTA;

INSERT INTO de_academy.bronze.web_events VALUES
  ('e1', current_timestamp(), 'u1', 'page_view'),
  ('e2', current_timestamp(), 'u2', 'purchase');

DESCRIBE HISTORY de_academy.bronze.web_events;
```

Learning outcome:

- You used Unity Catalog naming and created a Delta table.
- You verified Delta table history.

### Lab 2: Identify the Right Compute

For each scenario, choose compute:

1. Analyst dashboard with SQL queries: serverless SQL warehouse.
2. Daily PySpark notebook ETL using supported APIs: serverless jobs compute.
3. Spark submit JAR with custom libraries: classic jobs compute.
4. Interactive R notebook: classic all-purpose compute.
5. Declarative ETL pipeline with no special customization: serverless pipeline compute.

### Lab 3: Compare Table and Governance Roles

```sql
-- Delta table operation
DESCRIBE HISTORY de_academy.bronze.web_events;

-- Unity Catalog access operation
GRANT SELECT ON TABLE de_academy.bronze.web_events TO `analysts`;
```

Learning outcome:

- `DESCRIBE HISTORY` is about Delta table history.
- `GRANT SELECT` is about Unity Catalog governance.

## Official References

- Databricks Certified Data Engineer Associate Exam Guide, May 4, 2026: https://www.databricks.com/sites/default/files/2026-03/databricks-certified-data-engineer-associate-exam-guide-may-4-2026.pdf
- What is Databricks?: https://docs.databricks.com/aws/en/introduction
- High-level architecture: https://docs.databricks.com/aws/en/getting-started/high-level-architecture
- What is Delta Lake in Databricks?: https://docs.databricks.com/aws/en/delta/
- What is Unity Catalog?: https://docs.databricks.com/aws/en/data-governance/unity-catalog
- Compute: https://docs.databricks.com/aws/en/compute/
- Compute selection recommendations: https://docs.databricks.com/aws/en/compute/choose-compute
- Serverless compute: https://docs.databricks.com/aws/en/compute/serverless/
- Serverless compute limitations: https://docs.databricks.com/aws/en/compute/serverless/limitations
- Photon: https://docs.databricks.com/gcp/en/compute/photon
- Databricks pricing: https://www.databricks.com/product/pricing

