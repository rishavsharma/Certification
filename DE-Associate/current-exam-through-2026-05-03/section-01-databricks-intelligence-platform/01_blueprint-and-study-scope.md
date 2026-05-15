# Section 1: Databricks Intelligence Platform (Current Exam Through May 3, 2026)

## Scope / Objectives (Current Blueprint Wording)

From the **CURRENT EXAM GUIDE** version live **through May 3, 2026**, Section 1 objectives are:  

- **Enable features that simplify data layout decisions and optimize query performance.**
- **Explain the value of the Data Intelligence Platform.**
- **Identify the applicable compute to use for a specific use case.**

This repo section focuses only on those three bullets (and closely related prerequisite concepts that Databricks docs treat as part of them).

## Terminology Drift Note (May 4, 2026 Update)

Databricks indicates a **new exam version goes live May 4, 2026**. This build targets the **pre-May 4, 2026** blueprint.

That said, Databricks platform terminology has already shifted in-product and in docs in ways you can see in the current exam window:

- **"Lakehouse Platform"** language often appears in older material; current docs and the exam guide emphasize the **Databricks Data Intelligence Platform**.
- **"Clusters"** is increasingly replaced by **"compute"** (for general compute) and **"SQL warehouses"** (for SQL-optimized compute).
- **"SQL endpoints"** is legacy terminology; current docs use **"SQL warehouses."**
- **"Auto Optimize"** is a retired umbrella term; current docs describe **optimized writes** and **auto compaction** as distinct behaviors.

You should be able to translate legacy terms to current docs quickly, because the exam can use either phrasing.

## Core Concepts You Must Be Fluent In

### 1) Data Layout and Query Performance (Most Tested in Section 1)

What Databricks means by "simplify data layout decisions and optimize query performance" is largely:

- Prefer **liquid clustering** over manual partitioning and `ZORDER` for most Delta table layouts.
- Use **predictive optimization** (especially for Unity Catalog managed tables) to automate `OPTIMIZE`/`VACUUM` and reduce manual maintenance.
- Understand **optimized writes** and **auto compaction** as small-file mitigations that improve downstream read efficiency and reduce operational toil.

This is covered in: `02_data-layout-and-query-performance.md`.

### 2) Platform Value and Core Components

Section 1 expects you to articulate what Databricks is and why teams adopt it:

- A unified platform for **data engineering, analytics/BI, and AI** with shared governance and shared storage abstractions.
- The **lakehouse** architectural pattern (open storage + transactional tables + governance + multiple compute modalities).
- Core platform components that appear across tasks: **Delta Lake**, **Unity Catalog**, **Databricks SQL**, workspace architecture (control plane vs compute plane), and platform performance features like **Photon**.

This is covered in: `03_platform-value-and-core-components.md`.

### 3) Compute Selection

Section 1 expects scenario-based selection of compute:

- **Serverless compute** vs **classic compute** (customer-managed) vs **SQL warehouses**.
- Interactive vs automated workloads: notebooks vs jobs vs pipelines vs BI.
- Warehouse types (serverless / pro / classic) and why you choose one.

This is covered in: `04_compute-selection-decision-guide.md`.

## Examples / Decision Tables (Study Plan)

Use this as an exam-oriented study checklist.

| Objective (Section 1) | What you should be able to do on exam day | Where to study |
|---|---|---|
| Enable features that simplify data layout decisions and optimize query performance | Given a table + workload pattern, choose liquid clustering vs partitions vs `ZORDER`; identify when predictive optimization is the right "set-and-forget" answer; recognize which behaviors address the small-file problem | `02_data-layout-and-query-performance.md` |
| Explain the value of the Data Intelligence Platform | Explain lakehouse benefits, governance, open formats, collaboration, reduced data movement; describe control plane vs compute plane at a high level | `03_platform-value-and-core-components.md` |
| Identify applicable compute for a use case | Choose serverless notebooks/jobs/pipelines vs classic compute vs SQL warehouses; choose SQL warehouse type; recognize common anti-patterns (all-purpose compute for scheduled ETL, SQL warehouse for heavy PySpark) | `04_compute-selection-decision-guide.md` |

## Common Exam Traps

- Treating **optimized writes/auto compaction** as a full replacement for `OPTIMIZE` (they help, but do not cover all layout needs and do not do `ZORDER`).
- Assuming **partitioning** is always best practice; current guidance commonly prefers **liquid clustering** for Delta layout tuning.
- Mixing up **serverless compute** (Databricks-managed) vs **classic compute** (customer cloud account) and what that implies for startup time, operational overhead, and some feature availability.
- Confusing **SQL warehouses** (SQL-optimized) with general-purpose compute for Spark jobs.

## Official Sources (Links)

- Exam guide PDF containing the "current exam guide through May 3, 2026" and Section 1 objectives:  
  https://www.databricks.com/sites/default/files/2026-03/databricks-certified-data-engineer-associate-exam-guide-may-4-2026.pdf
- What is Databricks (Data Intelligence Platform overview):  
  https://docs.databricks.com/aws/en/introduction
- High-level architecture (control plane vs compute plane):  
  https://docs.databricks.com/aws/en/getting-started/high-level-architecture
- Compute overview (serverless vs classic vs SQL warehouses):  
  https://docs.databricks.com/en/compute/index.html
