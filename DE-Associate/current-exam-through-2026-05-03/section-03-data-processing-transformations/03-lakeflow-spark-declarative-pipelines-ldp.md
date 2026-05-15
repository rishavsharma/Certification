# Section 3 Study Guide 03: Lakeflow Spark Declarative Pipelines (LDP)

## Scope / Objectives (Current exam through May 3, 2026)

Current-exam objectives (Section 3):
- "Emphasize the advantages of LDP (for ETL process in Databricks)."
- "Implement data pipelines using LDP."

What this guide covers for the exam:
- What LDP is and why it is used for ETL on Databricks.
- The core building blocks (pipelines, flows, streaming tables, materialized views, temporary views).
- How to implement pipelines in SQL and Python with the current API naming.
- Data quality expectations and pipeline observability (event log).

## Terminology Drift Note (Keep current-exam wording visible)

- The exam blueprint says "LDP". In Databricks documentation, the current product name is:
  - Lakeflow Spark Declarative Pipelines (also abbreviated as "SDP" in docs).
- Older study resources and some UI elements historically used "Delta Live Tables (DLT)" and the `dlt` Python module.
  - Databricks documents the rename and recommends moving to the `pyspark.pipelines` module imported as `dp`.
- Legacy syntax may appear in older pipelines:
  - `LIVE.<table>` virtual schema (deprecated/legacy) and "legacy publishing mode".

For exam questions, treat "DLT" references as LDP unless the question is explicitly testing legacy behavior.

## Core Concepts

### What LDP is (at the exam level)

Lakeflow Spark Declarative Pipelines is a framework for building batch and streaming ETL pipelines in SQL and Python, where you declare datasets (tables/views) and Databricks manages incremental execution and pipeline lifecycle.

Key exam-relevant outcomes:
- Declarative definition of datasets (the "what"), not imperative job orchestration (the "how").
- Managed pipeline execution (including incremental processing for streaming tables/materialized views).
- Built-in primitives for data quality (expectations) and observability (event logs).

### Pipeline building blocks

- Pipeline: the top-level ETL object you configure and run.
- Flow: an execution unit created from a declared dataset definition.
- Dataset types (the most tested):
  - Streaming table: a Delta table with extra support for streaming/incremental processing.
  - Materialized view: the (persisted) results of a query, updated by a pipeline.
  - Temporary view: query definition evaluated when referenced (pipeline-scoped view semantics).

### Why LDP is advantageous (common exam angles)

Advantages frequently implied in scenarios:
- Reliability and repeatability: pipeline-managed updates, structured run history.
- Incremental processing: streaming tables and materialized views are designed for incremental refresh patterns.
- Data quality visibility: expectations provide metrics and configurable behavior (track, drop, fail).
- Observability: pipeline event logs provide structured records for progress, expectations, and errors.
- Unified approach for batch and streaming transformations using the same dataset-declaration model.

## Examples / Decision Tables

### SQL vs Python in LDP

LDP supports SQL and Python sources in the same pipeline when needed.

| Need | Prefer | Why |
|---|---|---|
| Mostly SQL transformations, easy readability | SQL | Declarative DDL-like dataset creation statements. |
| Use custom logic, libraries, or DataFrame APIs | Python | Full expressiveness of Spark DataFrames, UDFs, and custom code where supported. |
| Mix where ingestion needs Python but transforms are SQL | Mixed | Docs explicitly support mixing SQL + Python in one pipeline. |

### Choosing dataset types (streaming table vs materialized view vs temporary view)

| Dataset type | Best for | Key notes (exam traps) |
|---|---|---|
| Streaming table | Incremental ingestion and transformations (stream semantics) | Implemented in pipelines; supports expectations; designed for streaming/incremental updates. |
| Materialized view | Persisted query results for downstream consumption | Updated by a pipeline; a materialized view created in Databricks SQL also has a backing pipeline. |
| Temporary view | Reusable query logic inside the pipeline | Calculated when queried; used to structure pipeline code; older `@view` decorator is now `@temporary_view`. |

### Minimal SQL example (streaming table with expectation)

```sql
CREATE OR REFRESH STREAMING TABLE bronze_events
  (CONSTRAINT valid_event_ts EXPECT (event_ts IS NOT NULL) ON VIOLATION DROP ROW)
AS
SELECT * FROM STREAM(raw.events_source);
```

Exam takeaways:
- `CREATE OR REFRESH STREAMING TABLE` indicates LDP streaming table semantics.
- Expectations use `CONSTRAINT <name> EXPECT (<boolean expr>)` and can drop/fail/track depending on configuration.

### Minimal Python example (current API naming)

```python
from pyspark import pipelines as dp

@dp.table(name="silver_customers")
@dp.expect("valid_age", "age BETWEEN 0 AND 120")
def silver_customers():
    return (
        spark.readStream.table("main.bronze.customers_raw")
        .dropna(subset=["customer_id"])
    )
```

Exam takeaways:
- Docs describe replacing `import dlt` with `from pyspark import pipelines as dp`.
- Expectations in Python use decorators such as `@dp.expect(...)`.

### Pipeline observability: querying the event log

Databricks provides an `event_log()` table-valued function for LDP-related event logs.

```sql
SELECT *
FROM event_log('<pipeline-id>')
WHERE event_type = 'flow_progress'
ORDER BY timestamp DESC
LIMIT 100;
```

Exam takeaways:
- Event logs are a primary observability primitive for LDP.
- Expectation metrics and run status details can be queried from event logs.

### Serverless vs classic pipeline compute (exam framing)

| Pipeline compute | Best for | Key notes |
|---|---|---|
| Serverless pipeline | Default/recommended path for many new pipelines | Databricks manages compute; serverless has requirements/limitations. |
| Classic pipeline | When workload requires explicit compute configuration or policy-governed settings | LDP runtime manages parts of lifecycle; not every attribute is user-settable. |

## Common Exam Traps

- Mixing old and new naming:
  - If a question references DLT, assume LDP unless it is explicitly about the legacy `LIVE` schema or legacy publishing mode.
- Confusing dataset types:
  - A materialized view is persisted and updated by a pipeline; a temporary view is not a persisted dataset and is evaluated when referenced.
- Treating LDP like "just a scheduled notebook":
  - LDP is declarative and produces flows; it is not only orchestration.
- Forgetting expectations behavior:
  - Expectations can be used to track metrics, drop invalid rows, or fail updates depending on the operator used.
- Overlooking legacy behaviors:
  - `LIVE` schema is a legacy/deprecated feature; new pipelines use default publishing mode.

## Official-Source Links (Databricks only)

- Lakeflow Spark Declarative Pipelines (overview): https://docs.databricks.com/aws/en/ldp/
- Rename note: "What happened to Delta Live Tables (DLT)?": https://docs.databricks.com/gcp/en/ldp/where-is-dlt
- Load data with Lakeflow Declarative Pipelines: https://docs.databricks.com/gcp/en/delta-live-tables/load
- Transform data with pipelines: https://docs.databricks.com/en/delta-live-tables/load-and-transform.html
- Manage data quality with pipeline expectations: https://docs.databricks.com/aws/en/ldp/expectations
- `event_log` table-valued function (query pipeline event logs): https://docs.databricks.com/aws/en/sql/language-manual/functions/event_log
- LIVE schema (legacy): https://docs.databricks.com/aws/en/ldp/live-schema
- Materialized views (concepts): https://docs.databricks.com/aws/en/ldp/materialized-views
- Configure classic compute for pipelines: https://docs.databricks.com/aws/dlt/configure-compute

