# Section 3 Study Guide 04: Delta DDL/DML + PySpark Aggregations and Metrics

## Scope / Objectives (Current exam through May 3, 2026)

Current-exam objectives (Section 3):
- "Identify DDL (Data Definition Language)/DML features."
- "Compute complex aggregations and Metrics with PySpark Dataframes."

What this guide covers for the exam:
- The most common Delta SQL DDL/DML statements and the "gotchas" that create wrong answers.
- How to think about correctness in aggregations (grouping, distinct counts, pivots, grouping sets, and windowed metrics) using PySpark DataFrames.

## Terminology Drift Note (Keep current-exam wording visible)

- Databricks tables are typically backed by Delta Lake. Many SQL references in Databricks docs say "Delta table" implicitly when describing supported features.
- Some SQL features in docs mention newer runtime-version gates. The current exam blueprint (through May 3, 2026) focuses on identifying the correct statement and behavior, not remembering exact runtime versions.

## Core Concepts

### DDL vs DML (quick exam definitions)

- DDL (Data Definition Language): defines or changes schema objects (tables, views, constraints, properties).
  - Examples: `CREATE TABLE`, `CREATE OR REPLACE TABLE`, `ALTER TABLE`, `DROP TABLE`.
- DML (Data Manipulation Language): inserts, updates, deletes, or merges data.
  - Examples: `INSERT INTO`, `UPDATE`, `DELETE`, `MERGE INTO`.

### Delta SQL DDL: creating and replacing tables

The exam commonly tests subtle differences between:
- `CREATE TABLE IF NOT EXISTS ...` (no-op if the table exists)
- `CREATE OR REPLACE TABLE ...` (replace if exists; create if not)

Key behavior points that show up in answer choices:
- Databricks SQL `CREATE TABLE [USING]` supports `REPLACE` and `IF NOT EXISTS`, but they cannot be used together.
- If `USING` is omitted in Databricks SQL, the default format is `DELTA`.

Practical exam pattern:
- If the question says "regardless of whether a table already exists with this name", that is a strong hint for `CREATE OR REPLACE TABLE ...`.

### Delta SQL DML: the four big ones

1. `INSERT INTO` (append rows)
2. `UPDATE` (change existing rows that match predicate)
3. `DELETE FROM` (remove rows that match predicate)
4. `MERGE INTO` (upsert: update/insert/delete based on match conditions)

`MERGE INTO` is frequently tested because it is the canonical upsert for Delta tables and has important semantics (for example, errors when multiple source rows match the same target row in an ambiguous update).

## Examples / Decision Tables

### DDL: "create empty table" vs "create from query"

| Need | Best statement shape | Notes |
|---|---|---|
| Create an empty table with schema | `CREATE TABLE ... (cols...)` | Define columns explicitly. |
| Create a table from a query result | `CREATE TABLE ... AS SELECT ...` (CTAS) | Convenient but has limitations for some table specifications. |
| Replace a table and its content safely | `CREATE OR REPLACE TABLE ... AS SELECT ...` or `REPLACE TABLE ...` | Docs recommend replace over drop+recreate for Delta. |

#### Example: create or replace an empty Delta table

```sql
CREATE OR REPLACE TABLE main.hr.employee_ratings (
  employeeId STRING,
  startDate DATE,
  avgRating FLOAT
);
```

Exam takeaways:
- `CREATE OR REPLACE` handles both "exists" and "does not exist".
- By default, Databricks tables are Delta-backed unless specified otherwise.

### DML: inserting a single record

```sql
INSERT INTO my_table VALUES ('a1', 6, 9.4);
```

Exam takeaways:
- `INSERT INTO <table> VALUES (...)` is the canonical append pattern.
- Wrong answers often swap keyword order or use non-existent syntax.

### MERGE: common upsert skeleton

```sql
MERGE INTO target AS t
USING source AS s
ON t.id = s.id
WHEN MATCHED THEN UPDATE SET *
WHEN NOT MATCHED THEN INSERT *;
```

Exam takeaways:
- `MERGE` can update and insert in one atomic operation.
- An ambiguous update can occur if multiple source rows match one target row for the same key.

### Constraints and generated columns (DDL features you may see)

Constraints in Databricks docs include:
- `NOT NULL` constraints (defined in schema; can be altered).
- `CHECK` constraints (managed via `ALTER TABLE ... ADD/DROP CONSTRAINT`).
- Informational primary keys and foreign keys (Unity Catalog).

Generated columns include identity columns and expression-generated columns. In exam questions, these tend to appear as "which clause is valid in CREATE TABLE" or "what is supported with CTAS".

## PySpark Aggregations and Metrics (Correctness-first)

### The GroupedData mental model

In PySpark DataFrames:
- `df.groupBy(...)` returns a `GroupedData`.
- You then compute metrics with `.agg(...)` or convenience methods like `.count()`, `.sum(...)`, `.avg(...)`.

#### Example: multiple metrics in a single aggregation

```python
from pyspark.sql import functions as F

daily = (
  billing_df
  .groupBy("billing_date")
  .agg(
    F.sum("amount_billed").alias("total_revenue"),
    F.count_distinct("billing_id").alias("total_invoices"),
  )
)
```

Exam takeaways:
- Use `count_distinct` (or `countDistinct`) for distinct counts.
- Be precise about what is being counted (invoice id vs patient id vs row count).

### Pivot: avoid accidental eager distinct computation

```python
pivoted = (
  df.groupBy("year")
    .pivot("course", ["dotNET", "Java"])   # provide explicit values when possible
    .sum("earnings")
)
```

Exam takeaways:
- When pivot values are not provided, Spark may eagerly compute distinct pivot values. This can be inefficient and can be referenced in performance-oriented scenario traps.

### Grouping sets: multi-dimensional aggregation in PySpark

```python
from pyspark.sql import functions as F

g = (
  df.groupingSets(
    [("city", "car_model"), ("city",), ()],
    "city", "car_model",
  )
  .agg(F.sum(F.col("quantity")).alias("sum_qty"))
)
```

Exam takeaways:
- Grouping sets let you compute subtotals and grand totals in one pass (conceptually similar to SQL `GROUPING SETS`, `CUBE`, `ROLLUP`).
- Expect NULLs in subtotal rows for dimensions not present at that grouping level.

### Windowed metrics (moving sums/averages)

```python
from pyspark.sql import Window, functions as F

w = Window.partitionBy("customer_id").orderBy("event_ts").rowsBetween(Window.currentRow - 6, Window.currentRow)
df_with_7d = df.withColumn("sum_7_rows", F.sum("amount").over(w))
```

Exam takeaways:
- `rowsBetween` is row-based, not time-based. If a question wants "last 7 days", you need a range/time-based approach, not a fixed number of rows.

## Common Exam Traps

### Delta DDL/DML traps

- `CREATE OR REPLACE TABLE IF NOT EXISTS` is invalid (mutually exclusive semantics).
- Confusing `INSERT INTO` with `UPDATE`:
  - `UPDATE` modifies existing rows; it does not append new rows.
- Picking `MERGE` without considering duplicate keys in the source:
  - If multiple source rows match the same target row, merge can fail due to ambiguous updates.
- Forgetting `DELETE` predicate semantics:
  - `DELETE FROM table` with no predicate deletes all rows.

### Aggregation correctness traps

- Counting the wrong identifier:
  - "number of invoices" usually means distinct invoice IDs, not distinct customers and not row count.
- `count(col)` vs `count("*")`:
  - `count(col)` ignores nulls; `count("*")` counts rows.
- Distinct vs approximate distinct:
  - `approx_count_distinct` is approximate; only choose it when the scenario allows approximation for speed/cost.
- Pivot without specifying values:
  - Can cause extra computation to discover distinct pivot values.
- Row-based windows vs time-based requirements:
  - If asked for "rolling 7-day revenue", do not use `rowsBetween` with 7 rows unless the data is exactly one row per day per partition.

## Official-Source Links (Databricks only)

Delta SQL DDL/DML:
- CREATE TABLE [USING]: https://docs.databricks.com/aws/en/sql/language-manual/sql-ref-syntax-ddl-create-table-using
- INSERT INTO: https://docs.databricks.com/aws/en/sql/language-manual/sql-ref-syntax-dml-insert-into
- DELETE FROM: https://docs.databricks.com/gcp/en/sql/language-manual/delta-delete-from
- MERGE INTO (upsert): https://docs.databricks.com/aws/en/delta/merge
- Constraints on Databricks: https://docs.databricks.com/gcp/en/tables/constraints
- Delta Lake generated columns: https://docs.databricks.com/aws/en/delta/generated-columns

PySpark aggregation references:
- GroupedData overview: https://docs.databricks.com/gcp/en/pyspark/reference/classes/groupeddata
- `agg` (GroupedData): https://docs.databricks.com/aws/en/pyspark/reference/classes/groupeddata/agg
- `pivot` (GroupedData): https://docs.databricks.com/aws/en/pyspark/reference/classes/groupeddata/pivot
- `groupingSets` (DataFrame): https://docs.databricks.com/aws/en/pyspark/reference/classes/dataframe/groupingSets
- `count_distinct`: https://docs.databricks.com/aws/en/pyspark/reference/functions/count_distinct
- `approx_count_distinct`: https://docs.databricks.com/aws/en/pyspark/reference/functions/approx_count_distinct
- `collect_set`: https://docs.databricks.com/aws/en/pyspark/reference/functions/collect_set
- `rowsBetween` (Window): https://docs.databricks.com/aws/en/pyspark/reference/classes/window/rowsBetween

