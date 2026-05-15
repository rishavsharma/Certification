# Compute Selection Decision Guide (Section 1)

## Scope / Objectives (Current Blueprint Wording)

This guide supports the Section 1 objective:

- **Identify the applicable compute to use for a specific use case.**

On the current exam (live through **May 3, 2026**), compute questions are typically scenario-based and reward picking the simplest supported option, which is often serverless.

## Terminology Drift Note (Clusters vs Compute)

Databricks materials may use:

- **Cluster** (legacy/common) to mean Spark compute
- **All-purpose cluster** (legacy) -> **classic all-purpose compute**
- **Job cluster** (legacy) -> **classic jobs compute** (or serverless compute for jobs)
- **SQL endpoint** (legacy) -> **SQL warehouse**

In current docs, "**compute**" is the umbrella term: **serverless compute**, **classic compute**, and **SQL warehouses**.

## Core Concepts

### 1) The Three Big Buckets

1. **Serverless compute**
   - Databricks-managed on-demand compute
   - Intended to reduce setup/management and improve startup/elasticity
   - Exists for notebooks, jobs, and pipelines (and SQL warehouses as serverless warehouses)

2. **Classic compute**
   - Compute you create and manage; runs in your cloud account
   - Includes classic all-purpose compute and classic jobs compute (and classic pipeline compute)
   - Used when you need custom configuration or a capability not available on serverless

3. **SQL warehouses**
   - SQL-optimized compute for Databricks SQL workloads (queries, dashboards, BI)
   - Comes in types (serverless, pro, classic) with different capability/cost/behavior tradeoffs

### 2) Interactive vs Automated Is Usually the First Split

Most "pick compute" questions can be answered by:

- Interactive exploration in notebooks:
  - Prefer **serverless compute for notebooks** (unless you have a reason to choose classic)
  - Or attach notebook to a **SQL warehouse** if it is primarily SQL analytics
- Automated workloads (scheduled ETL, recurring pipelines):
  - Prefer **serverless compute for jobs/pipelines** when supported
  - Use **classic jobs/pipeline compute** when you need unsupported features/customization

### 3) SQL Warehouse Types (Serverless vs Pro vs Classic)

At a high level:

- **Serverless SQL warehouse**: typically recommended; best startup and elasticity; includes extra management/performance features (for example intelligent workload management).
- **Pro SQL warehouse**: use when serverless is not available or when you require custom networking/hybrid/federation patterns that require compute in your cloud account.
- **Classic SQL warehouse**: entry-level; fewer performance capabilities than serverless/pro; often used when other options are not available.

## Examples / Decision Tables

### A) One-Table Cheat Sheet (Most Exam-Useful)

| Use case | Best default answer | When to choose something else |
|---|---|---|
| Interactive notebook (Python/SQL exploration) | **Serverless compute** | Choose classic all-purpose compute if you need R language support or RDD APIs; choose SQL warehouse if notebook is SQL-focused |
| Scheduled Spark job (ETL, batch transforms) | **Serverless compute for jobs** | Choose classic jobs compute if you need custom cluster settings or a feature not supported in serverless |
| Lakeflow pipeline (declarative pipelines) | **Serverless pipelines** | Choose classic pipeline compute if serverless is unsupported for a needed capability or you are constrained by legacy metastore scenarios |
| BI dashboards / SQL queries / reporting | **Serverless SQL warehouse** | Choose pro for custom networking/hybrid/federation needs; choose classic for entry-level scenarios when others are unavailable |
| Ad hoc SQL exploration | **SQL warehouse** (often serverless) | Use classic/pro if serverless isn't available/allowed |

### B) "If the Question Mentions..." Mapping

| Keyword(s) in prompt | Most likely compute category | Why |
|---|---|---|
| "Dashboard", "BI", "SQL query performance", "Databricks SQL" | SQL warehouse | Purpose-built SQL compute and SQL tooling |
| "Job", "scheduled", "orchestration", "pipeline automation" | Serverless jobs or classic jobs compute | Automated workloads; serverless recommended when supported |
| "Notebook exploration", "data scientist", "interactive" | Serverless notebooks or classic all-purpose | Interactive patterns; serverless typically preferred |
| "Need custom networking", "hybrid", "federation", "custom settings" | Classic compute or Pro SQL warehouse | Indicates requirements beyond serverless defaults |

### C) SQL Warehouse Type Selection (Mini-Decision Table)

| Requirement | Choose | Rationale |
|---|---|---|
| Fast startup and rapid autoscaling for variable query demand | **Serverless** | Best responsiveness and managed elasticity |
| Serverless not supported in region or not enabled | **Pro** (or classic) | Pro is typical non-serverless default in UI in many cases |
| Custom networking needed for federation/hybrid connectivity | **Pro** | Compute lives in your cloud account (more control of networking) |
| Entry-level interactive SQL, lowest-feature baseline | **Classic** | Fewer performance capabilities than serverless/pro |

## Common Exam Traps

- Using **classic all-purpose compute** for scheduled ETL "because it works." Databricks guidance commonly recommends serverless for automated workloads when supported.
- Picking a **SQL warehouse** for a Spark job that uses Python transformations, UDFs, or complex distributed compute patterns. Warehouses are SQL-optimized and best for SQL workloads.
- Confusing **serverless compute** with "no cost." Serverless reduces management overhead, but it is still billed.
- Ignoring the "legacy metastore" hint. Several serverless features have constraints when a workspace is tied to legacy/external metastores; prompts sometimes include this clue.

## Official Sources (Links)

- Compute overview (serverless vs classic vs SQL warehouses):  
  https://docs.databricks.com/en/compute/index.html
- Compute selection recommendations (tables mapping workloads to compute types):  
  https://docs.databricks.com/gcp/en/compute/choose-compute
- Classic compute overview (what classic compute is and where it runs):  
  https://docs.databricks.com/aws/en/compute/use-compute
- Notebook compute resources (notebooks can run on serverless, classic all-purpose, or SQL warehouses):  
  https://docs.databricks.com/aws/en/notebooks/notebook-compute
- SQL warehouse types (serverless / pro / classic capabilities and guidance):  
  https://docs.databricks.com/aws/en/compute/sql-warehouse/warehouse-types
- Connect to serverless compute (definition and why it is "versionless" and Databricks-managed):  
  https://docs.databricks.com/aws/en/admin/workspace-settings/serverless
