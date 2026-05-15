# Data Layout and Query Performance (Section 1)

## Scope / Objectives (Current Blueprint Wording)

This guide supports the Section 1 objective:

- **Enable features that simplify data layout decisions and optimize query performance.**

In this exam version (live through **May 3, 2026**), Databricks guidance heavily emphasizes **liquid clustering** and **predictive optimization** for Delta tables governed by Unity Catalog.

## Terminology Drift Note

You will see older training content refer to:

- **Partitioning + `ZORDER`** as the primary layout strategy.
- "**Auto Optimize**" as a single feature.

Current docs normalize this as:

- Prefer **liquid clustering** (replaces many uses of partitioning and `ZORDER`).
- "Auto Optimize" is a retired umbrella term; think **optimized writes** and **auto compaction** as separate small-file mitigations, plus **`OPTIMIZE`** (often automated via **predictive optimization**).

## Core Concepts

### 1) What "Data Layout" Means on Databricks (Delta Tables)

"Layout" is how rows end up distributed across data files. Layout impacts query performance because:

- Data engines can skip reading files if file-level stats (min/max, etc.) prove they cannot match filters (**data skipping**).
- Too many small files adds overhead (listing, metadata, planning, scheduling, IO amplification).
- Rewriting files (`OPTIMIZE`) can compact small files and physically group related rows to improve skipping.

Delta tables store:

- Parquet data files in object storage
- A Delta transaction log that provides ACID and table metadata

### 2) Liquid Clustering (Modern Default for Layout Decisions)

Liquid clustering is a Delta Lake data layout technique designed to replace:

- Hive-style partitioning for many tables
- `OPTIMIZE ... ZORDER BY (...)` for many workloads

Key behaviors you need to know:

- You define clustering keys with `CLUSTER BY (...)`, or you let Databricks choose with **automatic liquid clustering** using `CLUSTER BY AUTO`.
- `OPTIMIZE` groups data by the clustering keys to improve pruning/skipping.
- You can change clustering keys later with `ALTER TABLE ... CLUSTER BY (...)`, but existing data is not automatically rewritten until you run `OPTIMIZE`.

Practical key selection heuristics:

- Choose columns commonly used in **WHERE filters** and **join keys**.
- Avoid extremely high-cardinality "random" keys that don't correlate with query filters.
- Too many clustering keys can hurt for smaller tables; automatic clustering can help avoid bad manual choices.

### 3) Predictive Optimization (Automated Maintenance for UC Managed Tables)

Predictive optimization is a Databricks-managed automation layer for **Unity Catalog managed tables** that:

- Detects which tables benefit from maintenance and schedules operations
- Automatically runs maintenance operations (including `OPTIMIZE` and `VACUUM`) on a cadence determined by observed workload and table changes
- Uses **serverless compute for jobs** for these maintenance tasks (and billing follows serverless managed services guidance)

From an exam perspective, treat predictive optimization as the "set-and-forget" answer for:

- Reducing the need to hand-schedule `OPTIMIZE`/`VACUUM`
- Keeping file sizes and layout healthy over time for managed tables

### 4) Optimized Writes and Auto Compaction (Small-File Mitigation)

Small files are a frequent performance killer, especially when:

- Ingest is micro-batch or streaming
- You write many partitions at once
- You have many concurrent writers

Databricks provides:

- **Optimized writes**: improves file sizing at write time (often by shuffling to write fewer, larger files)
- **Auto compaction**: compacts small files after writes (depending on runtime/table context)

Important nuance (exam trap): optimized writes and auto compaction **help** with small files but are **not a full replacement** for `OPTIMIZE` when you need deeper layout rewrites or clustering.

### 5) `OPTIMIZE` and (Legacy) `ZORDER`

`OPTIMIZE` rewrites data files to improve layout.

- With **liquid clustering enabled**, `OPTIMIZE` clusters by the liquid clustering keys.
- Without liquid clustering, `OPTIMIZE` can optionally use `ZORDER BY (...)` for multi-dimensional clustering, but Databricks commonly recommends liquid clustering instead of relying on `ZORDER`.

## Examples / Decision Tables

### A) Quick Decision Table: What to Use When

| Problem / Scenario | Most likely best feature | Why |
|---|---|---|
| "We keep arguing about partition columns and tuning `ZORDER`" | **Liquid clustering** (often `CLUSTER BY AUTO`) | Reduces manual tuning; replaces many partition and `ZORDER` decisions |
| "Queries filter heavily by 1 to 4 columns and the table is large" | **Liquid clustering** with appropriate keys | Improves data skipping for common filters |
| "Lots of small files after frequent writes" | **Optimized writes** and **auto compaction** | Reduce the small-file problem without manual `OPTIMIZE` scheduling |
| "We want maintenance to run automatically on UC managed tables" | **Predictive optimization** | Automates `OPTIMIZE`/`VACUUM` decisioning and execution |
| "We need a one-time cleanup or layout rewrite now" | `OPTIMIZE` (and clustering keys if applicable) | Immediate compaction and/or reclustering |

### B) Enabling Liquid Clustering (SQL)

```sql
-- Create a table with automatic liquid clustering.
CREATE OR REPLACE TABLE main.sales.fact_orders (
  order_id BIGINT,
  customer_id BIGINT,
  order_date DATE,
  status STRING,
  amount DECIMAL(18,2)
)
CLUSTER BY AUTO;

-- Enable automatic liquid clustering on an existing table.
ALTER TABLE main.sales.fact_orders CLUSTER BY AUTO;

-- Switch to manual keys (example: common filters on order_date and status).
ALTER TABLE main.sales.fact_orders CLUSTER BY (order_date, status);

-- Recluster existing data to reflect new clustering keys.
OPTIMIZE main.sales.fact_orders;
```

### C) Checking Clustering Status

Common patterns you can recognize in the UI or via SQL:

```sql
DESCRIBE DETAIL main.sales.fact_orders;
SHOW TBLPROPERTIES main.sales.fact_orders;
```

Look for properties such as whether automatic clustering is enabled and which clustering columns are currently in effect.

### D) `OPTIMIZE` (and Legacy `ZORDER`) Patterns

```sql
-- Standard optimize (compaction + layout improvements).
OPTIMIZE main.sales.fact_orders;

-- Legacy approach for non-clustered tables (Databricks often recommends liquid clustering instead).
OPTIMIZE main.sales.fact_orders ZORDER BY (customer_id, order_date);
```

## Common Exam Traps

- **Partitioning reflex**: Choosing partitioning by default, even when liquid clustering is the better modern answer.
- **"Predictive optimization = free"**: It reduces manual work but runs maintenance using serverless compute and is billable.
- **Confusing responsibilities**:
  - Optimized writes/auto compaction target the small-file problem.
  - `OPTIMIZE` is a rewrite operation that can compact and reorganize files, and with liquid clustering it groups by clustering keys.
- **Assuming reclustering is automatic** after changing clustering keys. You often need `OPTIMIZE` to rewrite existing files.
- **Forgetting Unity Catalog scope**: Several recommendations in docs are specifically for **Unity Catalog managed tables** and may differ for external or legacy metastores.

## Official Sources (Links)

- Use liquid clustering for tables:  
  https://docs.databricks.com/aws/en/delta/clustering
- `CLUSTER BY` clause reference:  
  https://docs.databricks.com/aws/en/sql/language-manual/sql-ref-syntax-ddl-cluster-by
- Predictive optimization for Unity Catalog managed tables:  
  https://docs.databricks.com/en/optimizations/predictive-optimization.html
- Optimize data file layout (`OPTIMIZE`):  
  https://docs.databricks.com/aws/en/delta/optimize
- Configure Delta Lake to control data file size (optimized writes + auto compaction, and "Auto Optimize" retirement note):  
  https://docs.databricks.com/optimizations/auto-optimize.html
- When to partition tables on Databricks (partitioning guidance and modern recommendations):  
  https://docs.databricks.com/gcp/en/tables/partitions
