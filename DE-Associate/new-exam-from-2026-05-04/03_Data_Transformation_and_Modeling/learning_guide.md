# Section 3: Data Transformation and Modeling - Learning Guide

Exam guide version: NEW EXAM GUIDE, valid on or after 4-May-2026.

## Section Objectives

This section covers seven syllabus bullets from the NEW EXAM GUIDE:

1. Implement data cleaning by reading bronze tables with PySpark/SQL, cleaning nulls, standardizing data types, and writing to new silver tables.
2. Combine DataFrames with operations such as inner join, left join, broadcast join, multiple keys, cross join, union, and union all.
3. Manipulate columns, rows, and table structures by adding, dropping, splitting, renaming column names, applying filters, and exploding arrays.
4. Perform data deduplication operations and aggregate operations on DataFrames, such as count, approximate count distinct, mean, and summary.
5. Understand the basic tuning parameters `spark.sql.shuffle.partitions`, `spark.default.parallelism`, `spark.executor.memory`, `spark.driver.memory`, and `spark.sql.autoBroadcastJoinThreshold`, and re-measure performance.
6. Understand the difference between, and how to build, Gold layer objects such as materialized views, views, streaming tables, and tables for BI and analytics teams in Unity Catalog.
7. Apply data quality checks and validation rules to ensure reliable Silver and Gold datasets.

## Exam Mental Model

Section 3 is the core data engineering section. The exam usually gives a raw bronze dataset and asks how to produce a cleaner silver or curated gold object.

Use this pattern:

1. Read from bronze.
2. Clean and standardize values.
3. Deduplicate at the right business grain.
4. Join or union related datasets correctly.
5. Aggregate or model data for consumers.
6. Persist to the correct Unity Catalog object.
7. Apply data quality rules.
8. Tune only after measuring.

The exam is not asking you to memorize every PySpark method. It is testing whether you can choose the correct transformation and avoid common wrong patterns.

## Medallion Context

The exam guide does not repeat the medallion architecture bullet from the older guide, but Section 3 uses bronze, silver, and gold language directly.

| Layer | Purpose | Typical Operations |
| --- | --- | --- |
| Bronze | Raw or lightly parsed source data | Ingest source records, preserve source columns, add ingestion metadata |
| Silver | Cleaned, validated, conformed data | Cast types, remove invalid rows, dedupe, standardize names/codes, join reference data |
| Gold | Business-ready data products | Aggregations, dimensional models, views, materialized views, streaming tables, BI tables |

Exam focus:

- Bronze keeps raw source fidelity.
- Silver improves correctness and usability.
- Gold is built for analytics, BI, dashboards, reporting, and business semantics.

## Bronze to Silver Cleaning

### Read Bronze with PySpark

```python
from pyspark.sql import functions as F

bronze_orders = spark.table("de_academy.bronze.orders_raw")
```

### Clean Nulls and Standardize Types

```python
silver_orders = (
    bronze_orders
        .withColumn("order_id", F.trim(F.col("order_id")))
        .withColumn("customer_id", F.trim(F.col("customer_id")))
        .withColumn("order_ts", F.to_timestamp("order_ts"))
        .withColumn("order_date", F.to_date("order_ts"))
        .withColumn("amount", F.col("amount").cast("decimal(10,2)"))
        .filter(F.col("order_id").isNotNull())
        .filter(F.col("customer_id").isNotNull())
        .filter(F.col("amount") >= 0)
        .drop("_rescued_data")
)
```

### Write Silver as a Unity Catalog Delta Table

```python
(
    silver_orders.write
        .format("delta")
        .mode("overwrite")
        .option("overwriteSchema", "true")
        .saveAsTable("de_academy.silver.orders")
)
```

Exam focus:

- Use `withColumn` to add or replace a column.
- Use `cast`, `to_date`, and `to_timestamp` to standardize data types.
- Use `filter` or `where` to remove invalid rows.
- Use `dropna` or `fillna` when the business rule is simple.
- Write curated outputs to a new silver table rather than overwriting bronze.

### Equivalent SQL Cleaning

```sql
CREATE OR REPLACE TABLE de_academy.silver.orders AS
SELECT
  trim(order_id) AS order_id,
  trim(customer_id) AS customer_id,
  cast(order_ts AS TIMESTAMP) AS order_ts,
  cast(order_ts AS DATE) AS order_date,
  cast(amount AS DECIMAL(10, 2)) AS amount
FROM de_academy.bronze.orders_raw
WHERE order_id IS NOT NULL
  AND customer_id IS NOT NULL
  AND cast(amount AS DECIMAL(10, 2)) >= 0;
```

## Handling Nulls

### Drop Rows with Nulls

```python
required_cols = ["order_id", "customer_id", "order_ts"]

clean_orders = bronze_orders.dropna(subset=required_cols)
```

### Fill Nulls

```python
clean_customers = (
    spark.table("de_academy.bronze.customers_raw")
        .fillna({"country": "UNKNOWN", "marketing_opt_in": False})
)
```

### SQL Null Handling

```sql
SELECT
  customer_id,
  coalesce(country, 'UNKNOWN') AS country,
  coalesce(marketing_opt_in, false) AS marketing_opt_in
FROM de_academy.bronze.customers_raw;
```

Exam focus:

- Drop nulls only when null means invalid for the target dataset.
- Fill nulls when a default value is meaningful.
- Use `coalesce` in SQL for default values.
- Do not replace nulls blindly in fields where null carries business meaning.

## Joins

### Inner Join

Use an inner join when only matching rows should remain.

```python
orders = spark.table("de_academy.silver.orders")
customers = spark.table("de_academy.silver.customers")

customer_orders = (
    orders.join(customers, on="customer_id", how="inner")
)
```

SQL:

```sql
SELECT o.order_id, o.customer_id, c.customer_name, o.amount
FROM de_academy.silver.orders o
INNER JOIN de_academy.silver.customers c
  ON o.customer_id = c.customer_id;
```

### Left Join

Use a left join when every row from the left DataFrame must remain.

```python
orders_with_customer = (
    orders.join(customers, on="customer_id", how="left")
)
```

Exam example:

- Keep all orders even if customer enrichment is missing: left join.
- Keep only orders with valid customer records: inner join.

### Join on Multiple Keys

```python
order_lines = spark.table("de_academy.silver.order_lines")
returns = spark.table("de_academy.silver.returns")

returned_lines = (
    order_lines.join(
        returns,
        on=["order_id", "line_id"],
        how="left",
    )
)
```

### Broadcast Join

Broadcast a small lookup table to avoid shuffling a large table.

```python
from pyspark.sql.functions import broadcast

products = spark.table("de_academy.silver.products")
orders = spark.table("de_academy.silver.orders")

orders_enriched = orders.join(
    broadcast(products),
    on="product_id",
    how="left",
)
```

SQL hint:

```sql
SELECT /*+ BROADCAST(p) */
  o.order_id,
  p.category,
  o.amount
FROM de_academy.silver.orders o
LEFT JOIN de_academy.silver.products p
  ON o.product_id = p.product_id;
```

Exam focus:

- Broadcast the small side, not the large fact table.
- Broadcast is useful for small dimensions or lookups.
- If the "small" table is actually large, broadcast can hurt performance or fail.

### Cross Join

A cross join creates every combination of rows from both inputs.

```python
calendar = spark.table("de_academy.silver.calendar")
regions = spark.table("de_academy.silver.regions")

calendar_region_grid = calendar.crossJoin(regions)
```

Exam focus:

- Cross join has no join condition.
- It can create a very large result.
- Use it intentionally for grids, test matrices, or all-combination scenarios.

## Union and Union All

In Spark DataFrames, `union` returns all rows from both DataFrames. It does not remove duplicates like SQL `UNION` does in many SQL dialects.

```python
online_orders = spark.table("de_academy.silver.online_orders")
store_orders = spark.table("de_academy.silver.store_orders")

all_orders = online_orders.unionByName(store_orders)
```

Use `unionByName` when column order might differ but names match.

```python
all_orders = online_orders.unionByName(store_orders, allowMissingColumns=True)
```

If duplicate rows must be removed:

```python
deduped_orders = all_orders.dropDuplicates(["order_id"])
```

SQL:

```sql
-- Keeps duplicates
SELECT * FROM de_academy.silver.online_orders
UNION ALL
SELECT * FROM de_academy.silver.store_orders;

-- Removes duplicate full rows
SELECT * FROM de_academy.silver.online_orders
UNION
SELECT * FROM de_academy.silver.store_orders;
```

Exam focus:

- PySpark `union` keeps duplicates.
- SQL `UNION ALL` keeps duplicates.
- SQL `UNION` removes duplicate full rows.
- `unionByName` is safer when column order differs.

## Column, Row, and Table Manipulation

### Add or Replace Columns

```python
orders = (
    orders
        .withColumn("net_amount", F.col("amount") - F.col("discount_amount"))
        .withColumn("ingestion_date", F.current_date())
)
```

### Drop Columns

```python
orders_public = orders.drop("raw_payload", "_rescued_data")
```

### Rename Columns

```python
customers_clean = (
    spark.table("de_academy.bronze.customers_raw")
        .withColumnRenamed("cust_id", "customer_id")
        .withColumnRenamed("cust_nm", "customer_name")
)
```

SQL:

```sql
ALTER TABLE de_academy.silver.customers
RENAME COLUMN cust_nm TO customer_name;
```

### Split Columns

```python
customers = (
    customers_clean
        .withColumn("email_domain", F.split(F.col("email"), "@").getItem(1))
)
```

SQL:

```sql
SELECT
  email,
  split(email, '@')[1] AS email_domain
FROM de_academy.silver.customers;
```

### Filter Rows

```python
valid_orders = orders.filter(
    (F.col("amount") > 0) &
    (F.col("order_date") >= F.lit("2026-01-01"))
)
```

### Explode Arrays

```python
events = spark.table("de_academy.bronze.web_events")

event_tags = (
    events
        .select(
            "event_id",
            F.explode("tags").alias("tag"),
        )
)
```

SQL:

```sql
SELECT
  event_id,
  tag
FROM de_academy.bronze.web_events
LATERAL VIEW explode(tags) exploded_tags AS tag;
```

Exam focus:

- Use `explode` to turn array elements into separate rows.
- Use `split` for string tokenization.
- Use `drop` for column removal in DataFrames.
- Use `filter`/`where` for row-level filtering.

## Deduplication

### Drop Exact Duplicates

```python
deduped = orders.dropDuplicates()
```

### Drop Duplicates by Business Key

```python
deduped_orders = orders.dropDuplicates(["order_id"])
```

### Keep the Latest Record Per Key

```python
from pyspark.sql.window import Window

w = Window.partitionBy("order_id").orderBy(F.col("updated_at").desc())

latest_orders = (
    orders
        .withColumn("rn", F.row_number().over(w))
        .filter(F.col("rn") == 1)
        .drop("rn")
)
```

SQL:

```sql
CREATE OR REPLACE TABLE de_academy.silver.orders_latest AS
SELECT * EXCEPT (rn)
FROM (
  SELECT
    *,
    row_number() OVER (
      PARTITION BY order_id
      ORDER BY updated_at DESC
    ) AS rn
  FROM de_academy.bronze.orders_raw
)
WHERE rn = 1;
```

Exam focus:

- `dropDuplicates(["key"])` keeps an arbitrary row per key.
- Use a window function when you must keep a specific row, such as latest by timestamp.
- Dedupe grain should match the business key, not necessarily every column.

## Aggregations

### Count and Mean

```python
daily_metrics = (
    orders
        .groupBy("order_date")
        .agg(
            F.count("*").alias("order_count"),
            F.countDistinct("customer_id").alias("unique_customers"),
            F.approx_count_distinct("product_id").alias("approx_products"),
            F.mean("amount").alias("avg_order_amount"),
            F.sum("amount").alias("gross_revenue"),
        )
)
```

### Summary

```python
orders.select("amount").summary("count", "mean", "min", "25%", "50%", "75%", "max").show()
```

### SQL Aggregation

```sql
CREATE OR REPLACE TABLE de_academy.gold.daily_sales AS
SELECT
  order_date,
  count(*) AS order_count,
  count(DISTINCT customer_id) AS unique_customers,
  approx_count_distinct(product_id) AS approx_products,
  avg(amount) AS avg_order_amount,
  sum(amount) AS gross_revenue
FROM de_academy.silver.orders
GROUP BY order_date;
```

Exam focus:

- `count("*")` counts rows.
- `count("col")` counts non-null values of `col`.
- `countDistinct` gives exact distinct count.
- `approx_count_distinct` is faster and approximate, useful for large datasets when exact precision is not required.
- `summary()` returns descriptive statistics.

## Basic Performance Tuning

The exam names five settings. You need to know when they matter and how to re-measure after changing them.

### `spark.sql.shuffle.partitions`

Controls the default number of partitions for shuffle operations such as joins and aggregations.

```python
spark.conf.set("spark.sql.shuffle.partitions", "auto")
```

or:

```python
spark.conf.set("spark.sql.shuffle.partitions", "400")
```

Exam focus:

- Too few partitions can create large slow tasks.
- Too many partitions can create overhead from many tiny tasks.
- Databricks often recommends `auto` where supported.
- For streaming queries, checkpoint state can make partition changes more constrained.

### `spark.default.parallelism`

Controls default parallelism for low-level RDD operations and some operations where Spark needs a default task count.

```python
spark.conf.set("spark.default.parallelism", "200")
```

Exam focus:

- Most DataFrame SQL shuffle behavior is more directly affected by `spark.sql.shuffle.partitions`.
- Do not tune blindly. Measure task counts, stage time, and shuffle metrics.

### `spark.executor.memory`

Controls memory available to each Spark executor.

```text
spark.executor.memory 8g
```

Exam focus:

- Increase executor memory if executors are running out of memory and the workload genuinely needs more memory per executor.
- More memory is not always faster; it can reduce parallelism depending on cluster sizing.

### `spark.driver.memory`

Controls memory available to the Spark driver.

```text
spark.driver.memory 8g
```

Exam focus:

- Driver memory helps when the driver is overloaded, for example by collecting too much metadata or using `collect()` on large datasets.
- Do not use more driver memory to fix executor-side shuffle or task OOM issues.
- Avoid `collect()` on large DataFrames.

### `spark.sql.autoBroadcastJoinThreshold`

Controls the size threshold under which Spark can automatically broadcast a small table for joins.

```python
spark.conf.set("spark.sql.autoBroadcastJoinThreshold", 50 * 1024 * 1024)
```

Disable automatic broadcast:

```python
spark.conf.set("spark.sql.autoBroadcastJoinThreshold", -1)
```

Exam focus:

- Broadcast joins avoid shuffling the large side.
- Broadcast only small lookup/dimension tables.
- Explicit broadcast hints can override the automatic threshold.

### Re-Measure Performance

After changing a setting, measure again:

```python
import time

start = time.time()
daily_metrics.count()
elapsed = time.time() - start
print(f"Elapsed seconds: {elapsed:.2f}")
```

Also inspect:

- Job run duration.
- Spark UI stages.
- Shuffle read/write.
- Spill to disk.
- Skewed tasks.
- Number and duration of tasks.

Exam focus:

- Tune one variable at a time when possible.
- Use evidence from the Spark UI, query profile, or job run history.
- Do not assume a larger cluster or larger memory setting automatically fixes inefficient transformations.

## Gold Layer Objects in Unity Catalog

Gold objects serve BI, dashboards, business reporting, and analytics teams. The exam expects you to choose the right object type.

### Regular Table

A table physically stores data and is good for curated datasets that downstream teams query repeatedly.

```sql
CREATE OR REPLACE TABLE de_academy.gold.daily_sales AS
SELECT
  order_date,
  sum(amount) AS revenue,
  count(*) AS orders
FROM de_academy.silver.orders
GROUP BY order_date;
```

Best fit:

- Stable curated datasets.
- Dimensional facts and dimensions.
- Tables refreshed by a scheduled job.

### View

A view stores a query definition and does not store physical result data.

```sql
CREATE OR REPLACE VIEW de_academy.gold.vw_active_customers AS
SELECT
  customer_id,
  customer_name,
  country
FROM de_academy.silver.customers
WHERE is_active = true;
```

Best fit:

- Lightweight semantic layer.
- Security abstraction or simplified query.
- Logic that should always reflect current source tables.

Tradeoff:

- Querying a view recomputes the underlying query.
- If the query is expensive and queried often, use a materialized view or table.

### Materialized View

A materialized view stores precomputed query results and refreshes to stay up to date.

```sql
CREATE OR REFRESH MATERIALIZED VIEW de_academy.gold.mv_daily_sales AS
SELECT
  order_date,
  sum(amount) AS revenue,
  count(*) AS order_count
FROM de_academy.silver.orders
GROUP BY order_date;
```

Best fit:

- Expensive aggregations queried repeatedly.
- BI dashboards requiring faster response.
- Results that should remain correct when upstream dimension rows change.

Exam focus:

- A materialized view is not the same as a regular view.
- Materialized views store results and refresh.
- They are useful when precomputation improves query performance.

### Streaming Table

A streaming table is a Delta table with additional support for streaming or incremental processing in Lakeflow Declarative Pipelines or Databricks SQL.

```sql
CREATE OR REFRESH STREAMING TABLE de_academy.silver.events_clean
AS SELECT
  event_id,
  event_ts,
  user_id,
  event_type
FROM STREAM(de_academy.bronze.events_raw)
WHERE event_id IS NOT NULL;
```

Best fit:

- Append-heavy or streaming data.
- Incremental row processing.
- Low-latency transformations.

Tradeoff:

- A streaming table generally processes each input row once. If logic changes and old rows must be reprocessed, a full refresh may be required.
- Materialized views are better when correctness requires recomputing joins after dimensions change.

## Data Quality Checks and Validation Rules

Data quality rules ensure that silver and gold datasets are reliable.

### SQL Constraints and Filters

```sql
CREATE OR REPLACE TABLE de_academy.silver.orders_valid AS
SELECT *
FROM de_academy.bronze.orders_raw
WHERE order_id IS NOT NULL
  AND customer_id IS NOT NULL
  AND cast(amount AS DECIMAL(10,2)) >= 0;
```

### Quarantine Invalid Records

```python
orders = spark.table("de_academy.bronze.orders_raw")

valid_orders = orders.filter(
    F.col("order_id").isNotNull() &
    F.col("customer_id").isNotNull() &
    (F.col("amount").cast("double") >= 0)
)

invalid_orders = orders.subtract(valid_orders)

valid_orders.write.mode("overwrite").saveAsTable("de_academy.silver.orders")
invalid_orders.write.mode("overwrite").saveAsTable("de_academy.silver.orders_quarantine")
```

### Lakeflow Pipeline Expectations in SQL

```sql
CREATE OR REFRESH MATERIALIZED VIEW de_academy.silver.orders_clean
(
  CONSTRAINT valid_order_id EXPECT (order_id IS NOT NULL) ON VIOLATION DROP ROW,
  CONSTRAINT valid_amount EXPECT (amount >= 0) ON VIOLATION DROP ROW
)
AS
SELECT
  order_id,
  customer_id,
  cast(amount AS DECIMAL(10,2)) AS amount,
  cast(order_ts AS TIMESTAMP) AS order_ts
FROM de_academy.bronze.orders_raw;
```

### Lakeflow Pipeline Expectations in Python

```python
from pyspark import pipelines as dp
from pyspark.sql import functions as F

@dp.materialized_view(name="orders_clean")
@dp.expect_or_drop("valid_order_id", "order_id IS NOT NULL")
@dp.expect_or_drop("valid_amount", "amount >= 0")
def orders_clean():
    return (
        spark.table("de_academy.bronze.orders_raw")
            .withColumn("amount", F.col("amount").cast("decimal(10,2)"))
    )
```

Expectation actions to recognize:

- Retain invalid records and record metrics.
- Drop invalid records.
- Fail the pipeline update when invalid records are found.

Exam focus:

- Use data quality rules for silver and gold reliability.
- Drop invalid records when they must not reach consumers.
- Fail the pipeline when invalid data means the dataset should not be published.
- Quarantine records when they need investigation instead of deletion.

## Common Exam Traps

- Using a left join when the requirement says only matching records should remain.
- Using an inner join when all left-side records must be preserved.
- Broadcasting a large table instead of a small dimension table.
- Using cross join accidentally and creating an explosive row count.
- Assuming PySpark `union` removes duplicates. It does not.
- Using `dropDuplicates(["key"])` when the requirement says keep the latest record.
- Using `collect()` to process large data on the driver.
- Increasing driver memory to fix executor-side shuffle failures.
- Tuning `spark.sql.shuffle.partitions` without re-measuring.
- Building BI dashboards directly on bronze tables instead of curated silver/gold objects.
- Choosing a regular view for expensive dashboard aggregations that are queried repeatedly.
- Choosing a streaming table when changed dimension rows must recompute historical joined results.

## Mini Labs

### Lab 1: Bronze to Silver Cleaning

```python
from pyspark.sql import functions as F

orders = spark.table("de_academy.bronze.orders_raw")

silver_orders = (
    orders
        .withColumn("order_id", F.trim("order_id"))
        .withColumn("order_ts", F.to_timestamp("order_ts"))
        .withColumn("order_date", F.to_date("order_ts"))
        .withColumn("amount", F.col("amount").cast("decimal(10,2)"))
        .filter("order_id IS NOT NULL AND amount >= 0")
        .dropDuplicates(["order_id"])
)

silver_orders.write.mode("overwrite").saveAsTable("de_academy.silver.orders")
```

### Lab 2: Join and Aggregate for Gold

```python
from pyspark.sql.functions import broadcast, count, sum

orders = spark.table("de_academy.silver.orders")
products = spark.table("de_academy.silver.products")

daily_category_sales = (
    orders
        .join(broadcast(products), "product_id", "left")
        .groupBy("order_date", "category")
        .agg(
            count("*").alias("order_count"),
            sum("amount").alias("revenue"),
        )
)

daily_category_sales.write.mode("overwrite").saveAsTable("de_academy.gold.daily_category_sales")
```

### Lab 3: Create Gold Objects

```sql
CREATE OR REPLACE VIEW de_academy.gold.vw_recent_orders AS
SELECT *
FROM de_academy.silver.orders
WHERE order_date >= current_date() - INTERVAL 30 DAYS;

CREATE OR REFRESH MATERIALIZED VIEW de_academy.gold.mv_daily_sales AS
SELECT order_date, sum(amount) AS revenue
FROM de_academy.silver.orders
GROUP BY order_date;
```

## Official References

- Databricks Certified Data Engineer Associate Exam Guide, May 4, 2026: https://www.databricks.com/sites/default/files/2026-03/databricks-certified-data-engineer-associate-exam-guide-may-4-2026.pdf
- PySpark on Databricks: https://docs.databricks.com/aws/en/pyspark/
- PySpark basics: https://docs.databricks.com/aws/en/pyspark/basics
- DataFrame class reference: https://docs.databricks.com/aws/en/pyspark/reference/classes/dataframe
- `dropDuplicates` DataFrame reference: https://docs.databricks.com/aws/en/pyspark/reference/classes/dataframe/dropDuplicates
- PySpark functions reference: https://docs.databricks.com/aws/en/pyspark/reference/functions/
- `approx_count_distinct` function reference: https://docs.databricks.com/aws/en/pyspark/reference/functions/approx_count_distinct
- `broadcast` function reference: https://docs.databricks.com/aws/en/pyspark/reference/functions/broadcast
- What is the medallion lakehouse architecture?: https://docs.databricks.com/gcp/en/lakehouse/medallion.html
- Lakeflow Declarative Pipelines: https://docs.databricks.com/aws/en/ldp/
- Develop Lakeflow Declarative Pipelines: https://docs.databricks.com/aws/en/ldp/develop
- Materialized views: https://docs.databricks.com/aws/en/ldp/materialized-views
- Streaming tables: https://docs.databricks.com/aws/en/ldp/streaming-tables
- Manage data quality with pipeline expectations: https://docs.databricks.com/aws/en/ldp/expectations
- What is a view?: https://docs.databricks.com/aws/en/views/
- CREATE VIEW SQL reference: https://docs.databricks.com/aws/en/sql/language-manual/sql-ref-syntax-ddl-create-view
- CREATE TABLE SQL reference: https://docs.databricks.com/aws/en/sql/language-manual/sql-ref-syntax-ddl-create-table-using
- Adaptive query execution: https://docs.databricks.com/aws/en/optimizations/aqe
- Diagnose high I/O and shuffle in Spark stages: https://docs.databricks.com/aws/en/optimizations/spark-ui-guide/long-spark-stage-io

