# Section 3 Study Guide 01: Medallion Architecture (Bronze / Silver / Gold)

## Scope / Objectives (Current exam through May 3, 2026)

Current-exam objective (Section 3):
- "Describe the three layers of the Medallion Architecture and explain the purpose of each layer in a data processing pipeline."

What this guide covers:
- How Databricks defines the Bronze, Silver, and Gold layers and why each exists.
- How to decide what belongs in each layer (rules of thumb + exam-friendly patterns).
- Common mistakes that show up in exam scenarios.

## Terminology Drift Note (Keep current-exam wording visible)

- "Medallion Architecture" is also referred to in Databricks docs as "medallion lakehouse architecture".
- Some Databricks examples map Bronze/Silver/Gold layers to tables updated by Lakeflow Spark Declarative Pipelines (LDP). The exam objective is the architecture concept, not a specific tooling requirement.

## Core Concepts

### The goal: a layered path to trusted, query-ready data products

Databricks recommends organizing data pipelines into layered stages to:
- Preserve raw facts (replayability and auditing).
- Incrementally improve data quality and usability.
- Serve multiple downstream consumers without each team re-building the same cleansing logic.

In the exam, you should be able to:
- State what each layer is for.
- Describe typical transformations and data modeling work per layer.
- Explain why separation reduces risk (bad data isolation), cost (reusability), and complexity (clear contracts).

### Bronze layer (raw / landing)

Purpose:
- Capture data "as-is" from source systems with minimal transformation.
- Provide an immutable-ish record for troubleshooting, reprocessing, and lineage.

Typical characteristics:
- Append-heavy ingestion (batch or streaming).
- Schema may be wide, messy, evolving, or semi-structured.
- Minimal business logic. If you do anything, it is usually mechanical and reversible.

Common Bronze operations (exam-friendly):
- Add ingestion metadata columns (ingest timestamp, source file name, batch id).
- Basic parsing/normalization needed to make the data readable downstream (for example, parse JSON into columns), while keeping original raw payload where needed.

### Silver layer (clean / conformed)

Purpose:
- Turn raw records into clean, queryable datasets with consistent types and semantics.
- Apply data quality rules and standardization that downstream workloads should not repeat.

Typical characteristics:
- Deduplication, filtering invalid records, handling late/out-of-order data (when applicable).
- Conforming keys, timestamps, and units.
- Enrichment (joins to reference data), flattening or structuring semi-structured fields.

Common Silver operations (exam-friendly):
- De-duplicate by business key + latest timestamp.
- Standardize column names and data types.
- Apply data quality constraints (drop/fail/track invalid records depending on requirements).

### Gold layer (curated / business-ready)

Purpose:
- Serve analytics and reporting use cases: aggregates, KPIs, dimensional models, and curated "data products".
- Present data in forms optimized for consumption rather than transformation.

Typical characteristics:
- Stronger business semantics and tighter contracts.
- Pre-aggregations, metric tables, and curated views.
- Frequently includes a star schema or reporting-friendly wide tables (depending on the organization).

Common Gold operations (exam-friendly):
- Aggregate to business grain (daily revenue, retention, funnel metrics).
- Build curated tables for dashboards (facts + dimensions).
- Publish and document datasets with stable schemas.

### Separation-of-concerns cheat sheet (what goes where)

Use this as a fast exam heuristic:

| Operation / Data | Bronze | Silver | Gold |
|---|---:|---:|---:|
| Raw ingestion, minimal transforms, retain original payload | Yes | No | No |
| Enforce schema, standardize types, clean nulls, deduplicate | Sometimes (minimal) | Yes | No |
| Conform business keys, join reference data, enrich | No | Yes | Sometimes |
| Business logic and metric definitions (KPI rules) | No | Sometimes (shared rules) | Yes |
| Aggregations for dashboards and reporting | No | Sometimes (intermediate) | Yes |
| Strict, consumption-ready schema and contracts | No | Improving | Yes |

## Examples / Decision Tables

### Example: same "customer orders" data across layers

Bronze (`bronze_orders`):
- One row per raw source event or file row.
- Columns may include `raw_payload`, `ingest_ts`, `source_file`, and minimally parsed fields.

Silver (`silver_orders`):
- One row per order line with clean types, de-duplicated and standardized.
- Bad records handled by rule (drop, quarantine, or fail the pipeline depending on requirements).

Gold (`gold_daily_revenue`):
- Aggregated metrics like `order_date`, `total_revenue`, `order_count`, `unique_customers`.
- This dataset is designed for BI tools and business users.

### Decision table: where to implement data quality checks

| Need | Best layer | Why (exam rationale) |
|---|---|---|
| Track data quality metrics without blocking progress | Silver (or Bronze->Silver boundary) | Silver is where you standardize and validate data for reuse. Bronze remains as-is for replay. |
| Completely block downstream updates if critical constraints fail | Silver | Prevents corrupt data from becoming "trusted". |
| Validate KPI definitions (for example, revenue = sum of paid invoices only) | Gold | KPI rules belong in curated business logic. |

## Common Exam Traps

- Treating Bronze as "already cleaned": Bronze is typically minimal transformation. Cleaning belongs in Silver.
- Mixing business aggregates into Silver without justification: Silver can contain some derived fields, but Gold is where business-facing aggregates and KPIs usually live.
- Confusing "layer" with "storage format": Medallion is an architectural pattern; Delta is often used, but the layer concept is independent of a specific file format.
- Over-indexing on "exact" rules: The exam usually tests intent. If a scenario emphasizes audit/replay, Bronze; if it emphasizes standardization and shared reuse, Silver; if it emphasizes KPIs/analytics, Gold.

## Official-Source Links (Databricks only)

- Medallion lakehouse architecture (Bronze/Silver/Gold overview): https://docs.databricks.com/aws/en/lakehouse/medallion
- Lakeflow Spark Declarative Pipelines overview (often used to implement medallion-style pipelines): https://docs.databricks.com/aws/en/ldp/
