# Section 3 Quiz: Data Processing and Transformations (52 MCQs)

Choose the one best answer (A, B, C, or D) for each question.

## Questions

1. A data engineer wants to preserve the raw state of incoming data for replayability and auditing, with minimal cleanup or validation. Which Medallion layer is the best fit?
   A. Bronze
   B. Silver
   C. Gold
   D. None of the above

2. A team needs a business-facing dataset that powers dashboards and contains highly curated KPIs and aggregates optimized for analytics queries. Which Medallion layer is the best fit?
   A. Bronze
   B. Silver
   C. Gold
   D. None of the above

3. A table must be created empty with a specific schema, and the statement must succeed whether or not the table already exists with the same name. Which SQL statement best matches this requirement?
   A. CREATE TABLE IF NOT EXISTS my_table (...)
   B. CREATE TABLE my_table AS SELECT ...
   C. CREATE OR REPLACE TABLE my_table (...)
   D. CREATE TABLE my_table LIKE other_table

4. A data engineer needs to append a single new record to an existing Delta table `my_table`. Which command is correct?
   A. UPDATE VALUES ('a1', 6, 9.4) my_table
   B. UPDATE my_table VALUES ('a1', 6, 9.4)
   C. INSERT VALUES ('a1', 6, 9.4) INTO my_table
   D. INSERT INTO my_table VALUES ('a1', 6, 9.4)

5. A pipeline receives incremental updates for customer records. The target Delta table should update existing rows when keys match and insert new rows when keys do not match. Which statement is designed for this pattern?
   A. MERGE INTO
   B. DELETE FROM
   C. VACUUM
   D. OPTIMIZE

6. A data engineer needs to remove only the rows older than `2025-01-01` from a Delta table `events`. Which command is correct?
   A. DROP TABLE events WHERE date < '2025-01-01'
   B. DELETE FROM events WHERE date < '2025-01-01'
   C. TRUNCATE TABLE events WHERE date < '2025-01-01'
   D. REMOVE FROM events WHERE date < '2025-01-01'

7. A DataFrame `billing_df` contains one row per invoice. The requirement is to compute daily revenue and daily invoice count (distinct invoices). Which PySpark code best meets the requirement?
   A. billing_df.groupBy("billing_date").agg(F.sum("amount_billed").alias("total_revenue"), F.count("billing_id").alias("total_invoices"))
   B. billing_df.groupBy("billing_date").agg(F.sum("amount_billed").alias("total_revenue"), F.count_distinct("billing_date").alias("total_invoices"))
   C. billing_df.groupBy("billing_date").agg(F.sum("amount_billed").alias("total_revenue"), F.count_distinct("billing_id").alias("total_invoices"))
   D. billing_df.groupBy("billing_date").agg(F.sum("amount_billed").alias("total_revenue"), F.count_distinct("patient_id").alias("total_invoices"))

8. A DataFrame column `coupon_code` contains many NULLs. Which statement is true about `count` in aggregations?
   A. count("coupon_code") counts all rows, including NULL values in coupon_code.
   B. count("coupon_code") counts only rows where coupon_code is NOT NULL.
   C. count("*") counts only rows where every column is NOT NULL.
   D. count("*") is invalid in PySpark DataFrame aggregations.

9. A team needs an approximate distinct count for a very large dataset where an exact distinct count is too expensive. Which function is intended for this use case?
   A. count_distinct
   B. approx_count_distinct
   C. collect_set
   D. count_if

10. A DataFrame is pivoted by a column with high cardinality. The engineer wants to avoid Spark eagerly computing all distinct pivot values. Which approach best addresses this?
   A. Use pivot("col") without specifying values so Spark infers them automatically.
   B. Use pivot("col", ["v1", "v2", "v3"]) to provide explicit values.
   C. Replace pivot with distinct().collect() on the pivot column.
   D. Use groupBy on the pivot column only and avoid any aggregation.

11. A data engineer uses grouping sets to produce subtotals and a grand total. What is the expected behavior of dimension columns for subtotal rows?
   A. Dimension columns are dropped from the result schema.
   B. Dimension columns that are not part of that grouping level are typically NULL in subtotal rows.
   C. Dimension columns are filled with the string "TOTAL".
   D. Dimension columns are duplicated across all rows.

12. A requirement states: "Compute rolling 7-day revenue per customer." The engineer uses `Window.rowsBetween(-6, 0)` on data that can have multiple rows per day. What is the main correctness risk?
   A. rowsBetween creates a time-based window and will always represent exactly 7 days.
   B. rowsBetween is row-based, so it represents 7 rows, not 7 days.
   C. rowsBetween is invalid unless the DataFrame is sorted globally.
   D. rowsBetween can only be used with count().

13. A data engineer wants multiple metrics (sum, max, distinct count) in a single groupBy operation. Which method is designed to accept multiple aggregate expressions?
   A. select
   B. agg
   C. join
   D. withColumn

14. A team uses `collect_set` to gather distinct values per group and then needs deterministic ordering for downstream comparisons. Which is the best practice?
   A. Rely on the order returned by collect_set because it is deterministic.
   B. Apply a sort to the collected array (for example, sort_array(collect_set(...))).
   C. Replace collect_set with count_distinct to get ordering.
   D. Replace collect_set with approx_count_distinct to get ordering.

15. A data engineer needs a distinct count across a composite key made of two columns (`value1`, `value2`). Which is the correct approach?
   A. F.count_distinct("value1", "value2")
   B. F.count("value1", "value2")
   C. F.countDistinct("value1") + F.countDistinct("value2")
   D. F.count_distinct(F.struct("value1", "value2")) is required; multi-column count_distinct is not supported

16. A team needs to compute the number of rows per group. Which aggregation is the most direct for counting all rows, including rows containing NULLs?
   A. F.count("some_column")
   B. F.count("*")
   C. F.count_distinct("*")
   D. F.approx_count_distinct("*")

17. A pivot operation is required: "Sum earnings by year, with course values as separate columns." Which pattern is correct?
   A. df.pivot("course").groupBy("year").sum("earnings")
   B. df.groupBy("year").pivot("course").sum("earnings")
   C. df.groupBy("course").pivot("year").sum("earnings")
   D. df.groupBy("year").unpivot("course").sum("earnings")

18. A data engineer wants multi-dimensional aggregation with subtotals in one pass in PySpark DataFrames. Which API is designed for this?
   A. df.groupingSets(...)
   B. df.repartition(...)
   C. df.withWatermark(...)
   D. df.foreachBatch(...)

19. A Lakeflow Job task is a notebook that runs batch ETL. The engineer wants the recommended compute with minimal infrastructure management. Which compute is the best fit?
   A. Serverless jobs
   B. Classic all-purpose compute
   C. Classic SQL warehouse
   D. Dedicated all-purpose compute

20. A Lakeflow Job task runs a SQL query that populates a dashboard table. Which compute is the recommended fit?
   A. Serverless SQL warehouse
   B. Serverless jobs
   C. Classic jobs compute
   D. Classic all-purpose compute

21. A Lakeflow Job includes a Spark Submit task. Which compute is the recommended fit?
   A. Serverless jobs
   B. Classic jobs compute
   C. Pro SQL warehouse
   D. Serverless pipeline

22. A Lakeflow Job includes a Lakeflow Spark Declarative Pipelines task. Which compute is the recommended fit?
   A. Classic all-purpose compute
   B. Serverless pipeline
   C. Classic SQL warehouse
   D. Dedicated all-purpose compute

23. Two analysts need to collaborate in the same notebook, iterating interactively on data exploration. Which compute family is the best fit?
   A. SQL warehouse
   B. Jobs compute
   C. All-purpose compute
   D. Pipeline compute

24. A production workflow is implemented as a Lakeflow Job. Which statement best reflects Databricks guidance for compute choice in this scenario?
   A. Use classic all-purpose compute for production jobs to minimize startup time.
   B. Use all-purpose compute because jobs compute cannot run notebooks.
   C. Prefer jobs compute (serverless or classic) rather than all-purpose compute for production jobs.
   D. Always use a SQL warehouse for production jobs.

25. A workspace is Unity Catalog enabled and multiple users need to run workloads concurrently on the same compute resource with strong user isolation. Which access mode is the recommended default?
   A. Standard access mode
   B. Dedicated access mode
   C. No isolation shared access mode
   D. Credential passthrough access mode

26. A user creates all-purpose compute and leaves access mode set to Auto. They choose a machine learning runtime. Which access mode behavior is most consistent with docs?
   A. Auto always selects Standard access mode regardless of runtime.
   B. Auto defaults to Standard, but can select Dedicated for ML runtimes.
   C. Auto always selects No isolation shared for ML runtimes.
   D. Auto selects access mode randomly to balance the cluster load.

27. A scenario explicitly requires Unity Catalog compatibility. Which access mode should generally be avoided because it is a legacy configuration that does not support Unity Catalog?
   A. Standard access mode
   B. Dedicated access mode
   C. No isolation shared access mode
   D. Auto access mode

28. A team needs to run R code on classic compute. Based on standard compute limitations, which access mode is the most likely to be required?
   A. Standard access mode
   B. Dedicated access mode
   C. Serverless pipeline
   D. SQL warehouse

29. A new pipeline must be created by a non-admin user who does not have classic compute creation permission. Which pipeline compute option best fits this constraint?
   A. Classic pipeline compute, because it requires no permissions
   B. Serverless pipeline, because classic compute creation permissions are not required
   C. Classic all-purpose compute, because pipelines run on interactive clusters
   D. Dedicated all-purpose compute, because it bypasses permission checks

30. A team selects a SQL warehouse type for interactive SQL exploration. They want better responsiveness and performance than classic warehouses. Which option best matches this goal?
   A. Classic SQL warehouse
   B. Pro or serverless SQL warehouse
   C. Classic all-purpose compute
   D. Classic jobs compute

31. An ETL workload has highly variable volume day-to-day. Which configuration choice most directly helps adapt resources to changing load?
   A. Disable autoscaling
   B. Enable autoscaling
   C. Disable auto-termination
   D. Use a fixed single-node cluster

32. Workspace admins want to restrict cluster configuration options and enforce cost controls for users who create compute resources. Which feature best matches this requirement?
   A. Compute policies
   B. Temporary views
   C. MERGE INTO
   D. Materialized views

33. In current Databricks documentation, which product name most directly corresponds to what was formerly called "Delta Live Tables (DLT)"?
   A. Lakeflow Spark Declarative Pipelines
   B. Lakeflow Connect
   C. Delta Sharing
   D. Unity Catalog

34. A data engineer is updating older Python pipeline code that uses `import dlt`. Which import is recommended in current docs?
   A. from databricks import dlt as dp
   B. from pyspark import pipelines as dp
   C. import live as dp
   D. from pyspark.sql import dlt as dp

35. A data engineer wants to define a view within a pipeline using Python and current naming. Which decorator should be used?
   A. @dp.view
   B. @dp.temporary_view
   C. @dp.materialized_view
   D. @dp.streaming_view

36. An expectation should retain invalid records but track pass/fail metrics. Which Python operator matches this behavior?
   A. dp.expect
   B. dp.expect_or_drop
   C. dp.expect_or_fail
   D. dp.drop_expectation

37. An expectation should drop invalid records before writing to the target dataset. Which Python operator matches this behavior?
   A. dp.expect
   B. dp.expect_or_drop
   C. dp.expect_or_fail
   D. dp.expect_drop_row

38. An expectation should fail the update when invalid records are detected. Which Python operator matches this behavior?
   A. dp.expect
   B. dp.expect_or_drop
   C. dp.expect_or_fail
   D. dp.expect_and_fail_pipeline

39. In SQL, which clause best indicates "drop invalid rows" for a pipeline expectation?
   A. EXPECT (...) ON ERROR SKIP ROW
   B. EXPECT (...) ON VIOLATION DROP ROW
   C. EXPECT (...) ON VIOLATION DROP TABLE
   D. EXPECT (...) ON VIOLATION IGNORE

40. A data engineer wants to monitor pipeline progress and expectation outcomes programmatically using SQL. Which feature is designed for querying LDP-related event logs without needing to know storage paths?
   A. DESCRIBE HISTORY
   B. event_log() table-valued function
   C. VACUUM
   D. CLUSTER BY

41. A data engineer creates a view over `event_log(TABLE(my_streaming_table))` and grants SELECT on that view to another user so they can query it. What is the most accurate expectation based on docs?
   A. The other user can query it because views can always be shared.
   B. The other user can query it only if the table is in the Hive metastore.
   C. The view cannot be shared this way; event_log access is restricted to the owner context.
   D. The other user can query it only if they are a workspace admin.

42. A pipeline query references `LIVE.my_bronze_table`. Which statement is most accurate?
   A. `LIVE` is the recommended schema for all new pipelines.
   B. `LIVE` is a legacy virtual schema associated with legacy publishing mode.
   C. `LIVE` is required for serverless pipelines.
   D. `LIVE` is the default Unity Catalog schema for pipelines.

43. A pipeline created in the default publishing mode contains SQL that references `LIVE.some_table`. What is the best expectation?
   A. The reference fails because `LIVE` is unsupported in default publishing mode.
   B. The reference is treated as a strict schema qualifier and always resolves to a separate database.
   C. The `LIVE` keyword is ignored and unqualified identifiers resolve in the current schema.
   D. The pipeline automatically migrates itself to legacy publishing mode.

44. Which statement about streaming tables is most accurate in Databricks docs?
   A. Streaming tables are regular views and are not persisted.
   B. Streaming tables are only supported in Lakeflow Spark Declarative Pipelines and in Databricks SQL with Unity Catalog.
   C. Streaming tables are only supported on all-purpose compute.
   D. Streaming tables require a SQL warehouse to parse and execute pipeline code.

45. A data engineer wants to declare a streaming table in SQL for a pipeline. Which statement form matches the LDP streaming table syntax?
   A. CREATE STREAM TABLE my_table AS SELECT ...
   B. CREATE OR REFRESH STREAMING TABLE my_table AS SELECT ...
   C. CREATE MATERIALIZED VIEW my_table AS SELECT ...
   D. CREATE TABLE my_table AS SELECT ... STREAMING

46. A user creates a materialized view in Databricks SQL. Which statement is most accurate about how it is refreshed?
   A. It is refreshed only when manually triggered by OPTIMIZE.
   B. Databricks creates a pipeline that is used to update the materialized view.
   C. It is refreshed only when queried by a user.
   D. It is refreshed by running VACUUM on the base table.

47. A pipeline uses an expectation with "fail" behavior. Which statement is most accurate about expectation metrics?
   A. Metrics are always recorded for fail expectations.
   B. Metrics are recorded only for fail expectations, not for warn or drop.
   C. Metrics are not recorded for fail expectations because the update fails when invalid records are detected.
   D. Metrics are recorded only if event_log() is disabled.

48. A pipeline has multiple parallel flows. One flow fails due to an `expect_or_fail` violation. What is the most accurate expectation?
   A. All flows fail immediately because any flow failure stops the entire pipeline.
   B. The failing flow fails; other flows do not necessarily fail due to that expectation.
   C. The pipeline automatically retries the failing flow until it succeeds.
   D. The pipeline deletes the target table and recreates it.

49. A pipeline needs Python-based ingestion for a source without SQL support, but downstream transformations are easier in SQL. What approach is supported?
   A. LDP supports only SQL; you must rewrite ingestion in SQL.
   B. LDP supports only Python; you must rewrite transformations in Python.
   C. LDP supports mixing SQL and Python sources within the same pipeline.
   D. You must build two separate pipelines, one SQL-only and one Python-only.

50. A team enables serverless pipelines for a new project. Which statement is most accurate about configuration?
   A. Serverless pipelines always use Unity Catalog and you cannot manually add a `clusters` object in pipeline JSON.
   B. Serverless pipelines require you to specify Spark version and node type in the `clusters` object.
   C. Serverless pipelines can only be used with the legacy Hive metastore.
   D. Serverless pipelines require legacy publishing mode to run.

51. A team switches a pipeline from serverless to classic compute to customize cluster settings. Which statement is most accurate?
   A. All cluster attributes are fully user-settable, including Spark version and cluster name.
   B. Some compute settings cannot be manually set because the LDP runtime manages parts of pipeline compute lifecycle.
   C. Classic compute removes the need for any pipeline permissions.
   D. Classic compute forces the pipeline to use legacy publishing mode.

52. A data engineer wants a single place to monitor execution progress, data quality expectation results, lineage, and errors for LDP runs. Which concept best matches this requirement?
   A. The pipeline event log
   B. VACUUM logs
   C. Table statistics
   D. Photon query plan

