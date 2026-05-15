# Section 3 Study Guide 02: Compute and Cluster Choice (Type + Configuration)

## Scope / Objectives (Current exam through May 3, 2026)

Current-exam objective (Section 3):
- "Classify the type of cluster and configuration for optimal performance based on the scenario in which the cluster is used."

What this guide focuses on for the exam:
- Picking the right compute family for the workload (Jobs, notebooks, SQL, pipelines).
- Understanding serverless vs classic compute tradeoffs at a high level.
- Access modes (terminology drift) and how they affect workload compatibility.
- Practical configuration choices that show up in scenario questions.

## Terminology Drift Note (Keep current-exam wording visible)

- The exam objective uses "cluster". Databricks documentation increasingly uses "compute" as the umbrella term and distinguishes:
  - All-purpose compute (interactive)
  - Jobs compute (job-scoped)
  - SQL warehouses (Databricks SQL)
  - Lakeflow Spark Declarative Pipelines compute (pipelines)
  - Serverless variants of the above
- Access mode naming changed in docs:
  - "Shared access mode" is now "Standard access mode"
  - "Single user access mode" is now "Dedicated access mode"
  - "No isolation shared" is a legacy access mode (not recommended)

For exam answers, prefer the intent (isolation, compatibility, performance, operational simplicity) over the exact old label.

## Core Concepts

### Step 1: Choose the correct compute family (by workload)

Databricks provides different compute types that are optimized for different workloads. In exam scenarios, start by identifying what is being run:
- Notebook (interactive development, exploration, ad hoc backfills)
- Job task (scheduled ETL, production batch)
- SQL query / dashboard (BI, ad hoc SQL analytics)
- Lakeflow Spark Declarative Pipelines (managed pipeline definitions, incremental tables/views)

If the question mentions "Lakeflow Jobs", "task types", or "recommended compute", the Jobs compute recommendations table in the docs is the anchor.

### Step 2: Decide serverless vs classic

High-level exam framing:
- Serverless: fastest path to "hands-off" compute. Databricks manages provisioning and scaling. Great when supported by the task type.
- Classic: you (or admins via policies) choose node types, sizing, some advanced cluster settings. Use when the workload requires it or when you need explicit control.

### Step 3: Ensure the access mode matches the security/governance context

Access mode (formerly "shared/single user") affects:
- User isolation and security behavior.
- Compatibility with Unity Catalog and certain cluster-level capabilities (libraries, init scripts, legacy behaviors).

Exam-safe heuristic (when Unity Catalog is in the scenario):
- Prefer Standard or Dedicated access mode.
- Avoid No isolation shared unless the scenario explicitly describes a legacy environment and no Unity Catalog requirements.

## Examples / Decision Tables

### Quick decision table: "What compute should I use?"

This table is intentionally exam-oriented (choose the best compute type given limited details).

| Scenario clue | Best first choice | Why |
|---|---|---|
| Scheduled ETL notebook task (batch) | Serverless jobs (if supported) | Minimal ops, Databricks-managed scaling for jobs. |
| Scheduled Spark Submit / JAR task | Classic jobs compute | Some task types require classic jobs compute. |
| SQL dashboard / BI workload | Serverless SQL warehouse (if available) | Optimized for SQL, lower operational overhead. |
| Building incremental ETL tables in a pipeline | Serverless pipeline (if available) | Purpose-built for pipelines; Databricks manages it. |
| Interactive exploration / collaboration | All-purpose compute | Designed for interactive use, multiple users attach. |
| Production job needs custom cluster config/policies | Classic jobs compute with policy | Admin-controlled configuration + repeatable settings. |

### Jobs task types and recommended compute (exam anchor)

Databricks docs provide a matrix of task types (notebook, Python wheel, SQL, pipelines, Spark submit, JAR) with recommended compute. In questions that resemble "which compute should you use for this task type?", align your answer with that matrix.

### Configuration decision table: common knobs

| Knob | Prefer when | Avoid when | Exam trap |
|---|---|---|---|
| Autoscaling | Variable workload, bursty jobs, cost optimization | Very predictable workloads where fixed sizing is simpler | Confusing autoscaling with "faster" always; it can add spin-up time. |
| Auto-termination | All-purpose compute used for exploration | Long-running pipelines/streaming jobs if it stops work | Assuming job clusters need auto-termination; job-scoped compute typically ends with the job run. |
| Photon acceleration | SQL-heavy ETL and analytics (when available) | Workloads with unsupported ops or where not enabled | Picking "Photon" as a universal answer without a scenario benefit. |
| Compute policies | Standardize cluster settings, control cost, restrict options | Ad hoc experimentation by admins | Not recognizing policy constraints in scenarios. |
| SQL warehouse type (serverless vs pro vs classic) | Serverless/pro for performance and responsiveness | Classic when only entry-level performance is needed | Assuming "classic" is best; docs note lower performance and longer startup. |

### Access mode selection (conceptual)

| Access mode (current naming) | What it is | Typical use |
|---|---|---|
| Standard access mode | Multi-user compute with strong isolation | Recommended default for most workloads |
| Dedicated access mode | Assigned to a single principal (user/group) | Use when a workload requires dedicated semantics |
| No isolation shared (legacy) | Multi-user, no isolation | Legacy only; not recommended |

## Common Exam Traps

- Confusing all-purpose compute with jobs compute:
  - All-purpose: interactive, collaborative.
  - Jobs compute: job-scoped, intended for production runs. Docs explicitly caution against using all-purpose compute for production jobs.
- Picking SQL warehouses for non-SQL workloads: SQL warehouses are for Databricks SQL; Spark jobs typically use jobs/all-purpose compute or pipelines.
- Ignoring task type limitations of serverless:
  - Serverless is not supported for every task type and has documented limitations.
- Mixing up old vs new access mode names:
  - If options include "shared" vs "standard", treat them as synonyms and select based on isolation/UC compatibility described.
- Assuming "No isolation shared" is acceptable with Unity Catalog:
  - Docs describe no-isolation shared as legacy and note limitations for Unity Catalog usage.

## Official-Source Links (Databricks only)

- Compute overview: https://docs.databricks.com/aws/en/compute/
- Configure compute for jobs (task type vs compute recommendations): https://docs.databricks.com/aws/en/jobs/compute
- Standard access mode overview: https://docs.databricks.com/aws/en/compute/standard-overview
- Standard compute requirements and limitations (access modes + limitations): https://docs.databricks.com/aws/en/compute/standard-limitations
- SQL warehouse types (serverless/pro/classic): https://docs.databricks.com/aws/en/compute/sql-warehouse/warehouse-types
