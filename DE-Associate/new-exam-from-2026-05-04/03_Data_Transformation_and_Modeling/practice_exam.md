# Section 3: Data Transformation and Modeling - Practice Exam

Exam guide version: NEW EXAM GUIDE, valid on or after 4-May-2026.

Question style: multiple-choice with four plausible options, modeled after the sample questions in the attached guide. These are original practice questions, not official exam questions.

## Questions

**Question 1**  
**Objective:** Implement data cleaning by reading bronze tables with PySpark/SQL  
A data engineer needs to create a silver table from `de_academy.bronze.orders_raw`. Rows without `order_id` must be removed, `amount` must be cast to `DECIMAL(10,2)`, and the result must be written to `de_academy.silver.orders`. Which PySpark code is most appropriate?

A. `spark.table("de_academy.bronze.orders_raw").filter("order_id IS NOT NULL").withColumn("amount", col("amount").cast("decimal(10,2)")).write.mode("overwrite").saveAsTable("de_academy.silver.orders")`  
B. `spark.table("de_academy.silver.orders").filter("order_id IS NULL").write.mode("append").saveAsTable("de_academy.bronze.orders_raw")`  
C. `spark.table("de_academy.bronze.orders_raw").drop("order_id").write.saveAsTable("de_academy.silver.orders")`  
D. `spark.sql("DROP TABLE de_academy.bronze.orders_raw")`

**Question 2**  
**Objective:** Implement data cleaning by reading bronze tables with PySpark/SQL  
A bronze customers table contains null `country` values. The business rule says null countries should become `UNKNOWN` in silver. Which PySpark method best implements this rule?

A. `.fillna({"country": "UNKNOWN"})`  
B. `.drop("country")`  
C. `.filter("country IS NULL")` only  
D. `.withColumnRenamed("country", "UNKNOWN")`

**Question 3**  
**Objective:** Implement data cleaning by reading bronze tables with PySpark/SQL  
A raw table has `event_ts` stored as a string. The silver table should store it as a timestamp. Which expression should the data engineer use?

A. `to_timestamp("event_ts")`  
B. `explode("event_ts")`  
C. `broadcast("event_ts")`  
D. `approx_count_distinct("event_ts")`

**Question 4**  
**Objective:** Implement data cleaning by reading bronze tables with PySpark/SQL  
A silver table should exclude negative order amounts. Which SQL filter is correct?

A. `WHERE cast(amount AS DECIMAL(10,2)) >= 0`  
B. `GROUP BY amount >= 0`  
C. `JOIN amount ON amount >= 0`  
D. `ORDER BY amount >= 0`

**Question 5**  
**Objective:** Implement data cleaning by reading bronze tables with PySpark/SQL  
A data engineer wants to preserve raw bronze data and write cleaned records to silver. Which design is best?

A. Read bronze, transform records, and write a new silver Delta table.  
B. Delete bronze immediately after the first successful read.  
C. Run BI dashboards directly on bronze without validation.  
D. Rename bronze to gold and skip cleaning.

**Question 6**  
**Objective:** Combine DataFrames with inner join  
A data engineer needs only orders that have a matching customer record. Which join type should be used?

A. Inner join  
B. Left join  
C. Cross join  
D. Union all

**Question 7**  
**Objective:** Combine DataFrames with left join  
A data engineer must keep every order row, even when no matching product record exists. Which PySpark join is correct?

A. `orders.join(products, "product_id", "left")`  
B. `orders.join(products, "product_id", "inner")`  
C. `orders.crossJoin(products)`  
D. `orders.unionByName(products)`

**Question 8**  
**Objective:** Combine DataFrames with broadcast join  
A fact table has 800 million rows and a product lookup table has 2,000 rows. Which code best reduces shuffle for the lookup join?

A. `fact.join(broadcast(products), "product_id", "left")`  
B. `broadcast(fact).join(products, "product_id", "left")`  
C. `fact.crossJoin(products)`  
D. `fact.union(products)`

**Question 9**  
**Objective:** Combine DataFrames with multiple keys  
A returns DataFrame should be matched to order lines by both `order_id` and `line_id`. Which PySpark join key expression is best?

A. `order_lines.join(returns, ["order_id", "line_id"], "left")`  
B. `order_lines.join(returns, "order_id,line_id", "left")`  
C. `order_lines.crossJoin(returns)`  
D. `order_lines.unionByName(returns)`

**Question 10**  
**Objective:** Combine DataFrames with cross join  
A data engineer needs every date in a calendar table paired with every region to create a planning grid. Which operation is appropriate?

A. `calendar.crossJoin(regions)`  
B. `calendar.join(regions, "date", "inner")`  
C. `calendar.unionByName(regions)`  
D. `calendar.dropDuplicates(["region"])`

**Question 11**  
**Objective:** Combine DataFrames with union and union all  
A PySpark engineer combines `online_orders` and `store_orders` with identical schemas and wants to keep all rows, including duplicates. Which method is appropriate?

A. `online_orders.unionByName(store_orders)`  
B. `online_orders.join(store_orders, "order_id", "inner")`  
C. `online_orders.crossJoin(store_orders)`  
D. `online_orders.dropDuplicates(["order_id"])`

**Question 12**  
**Objective:** Combine DataFrames with union and union all  
Two DataFrames have the same column names but different column order. Which operation is safest?

A. `df1.unionByName(df2)`  
B. `df1.union(df2)` without checking schema order  
C. `df1.crossJoin(df2)`  
D. `df1.join(df2)`

**Question 13**  
**Objective:** Manipulate columns by adding columns  
A data engineer wants to add `net_amount = amount - discount_amount` to an orders DataFrame. Which code is correct?

A. `orders.withColumn("net_amount", col("amount") - col("discount_amount"))`  
B. `orders.drop("net_amount", col("amount") - col("discount_amount"))`  
C. `orders.filter("net_amount = amount - discount_amount")`  
D. `orders.groupBy("net_amount")`

**Question 14**  
**Objective:** Manipulate columns by dropping columns  
A raw table includes `_rescued_data` and `raw_payload` columns that should not appear in the public silver table. Which PySpark command removes them?

A. `df.drop("_rescued_data", "raw_payload")`  
B. `df.filter("_rescued_data IS NULL")`  
C. `df.withColumn("_rescued_data", "raw_payload")`  
D. `df.groupBy("_rescued_data", "raw_payload")`

**Question 15**  
**Objective:** Manipulate columns by renaming columns  
A bronze table has a column `cust_id`, but the silver standard name is `customer_id`. Which method should be used?

A. `withColumnRenamed("cust_id", "customer_id")`  
B. `drop("customer_id")`  
C. `filter("cust_id = customer_id")`  
D. `explode("cust_id")`

**Question 16**  
**Objective:** Manipulate columns by splitting columns  
A customer table has an `email` column. The engineer wants a new `email_domain` column containing text after `@`. Which expression is appropriate?

A. `split(col("email"), "@").getItem(1)`  
B. `explode(col("email")).getItem("@")`  
C. `broadcast(col("email"), "@")`  
D. `approx_count_distinct(col("email"))`

**Question 17**  
**Objective:** Manipulate rows by filtering  
A data engineer wants rows where `amount > 0` and `order_date >= '2026-01-01'`. Which PySpark filter is valid?

A. `df.filter((col("amount") > 0) & (col("order_date") >= lit("2026-01-01")))`  
B. `df.groupBy((col("amount") > 0) & (col("order_date") >= lit("2026-01-01")))`  
C. `df.union((col("amount") > 0) & (col("order_date") >= lit("2026-01-01")))`  
D. `df.crossJoin(col("amount") > 0)`

**Question 18**  
**Objective:** Manipulate rows by exploding arrays  
A `web_events` table has an array column `tags`. The target table should have one row per event-tag pair. Which function should be used?

A. `explode("tags")`  
B. `split("tags", ",")`  
C. `broadcast("tags")`  
D. `mean("tags")`

**Question 19**  
**Objective:** Manipulate table structures  
A data engineer wants to rename a column in an existing Unity Catalog table using SQL. Which command pattern is appropriate?

A. `ALTER TABLE catalog.schema.table RENAME COLUMN old_name TO new_name`  
B. `SELECT old_name RENAME new_name FROM catalog.schema.table`  
C. `COPY INTO catalog.schema.table RENAME old_name`  
D. `OPTIMIZE catalog.schema.table RENAME old_name`

**Question 20**  
**Objective:** Perform data deduplication operations  
A silver table should contain one row per `order_id`, but any duplicate row can be kept. Which PySpark method is sufficient?

A. `dropDuplicates(["order_id"])`  
B. `drop("order_id")`  
C. `countDistinct("order_id")`  
D. `unionByName(["order_id"])`

**Question 21**  
**Objective:** Perform data deduplication operations  
A raw table has multiple records per `customer_id`; the silver table must keep the record with the latest `updated_at`. Which approach is best?

A. Use `row_number()` over a window partitioned by `customer_id` ordered by `updated_at` descending, then filter `rn = 1`.  
B. Use `dropDuplicates(["customer_id"])` because it always keeps the latest row.  
C. Use `countDistinct("customer_id")` and write the count as the silver table.  
D. Use `crossJoin` with the same table.

**Question 22**  
**Objective:** Perform aggregate operations on DataFrames  
A data engineer wants daily order counts and daily revenue. Which PySpark pattern is correct?

A. `orders.groupBy("order_date").agg(count("*").alias("order_count"), sum("amount").alias("revenue"))`  
B. `orders.filter("order_date").agg(count("*"), sum("amount"))`  
C. `orders.withColumn("order_date").agg(count("*"), sum("amount"))`  
D. `orders.unionByName("order_date").agg(count("*"), sum("amount"))`

**Question 23**  
**Objective:** Perform aggregate operations on DataFrames  
A very large dataset needs an estimate of unique users. Exact precision is not required. Which function is most appropriate?

A. `approx_count_distinct("user_id")`  
B. `mean("user_id")`  
C. `explode("user_id")`  
D. `broadcast("user_id")`

**Question 24**  
**Objective:** Perform aggregate operations on DataFrames  
A data engineer wants descriptive statistics for `amount`, including count, mean, min, quartiles, and max. Which DataFrame method is appropriate?

A. `df.select("amount").summary()`  
B. `df.select("amount").explode()`  
C. `df.select("amount").crossJoin()`  
D. `df.select("amount").broadcast()`

**Question 25**  
**Objective:** Perform aggregate operations on DataFrames  
What is the difference between `count("*")` and `count("customer_id")` in Spark aggregations?

A. `count("*")` counts rows; `count("customer_id")` counts non-null customer IDs.  
B. `count("*")` counts only nulls; `count("customer_id")` counts all rows.  
C. Both always return exact distinct customer counts.  
D. Both are only valid after a cross join.

**Question 26**  
**Objective:** Understand `spark.sql.shuffle.partitions`  
A groupBy aggregation creates too few large shuffle tasks, each running slowly. Which setting is directly relevant?

A. `spark.sql.shuffle.partitions`  
B. `spark.driver.memory` only  
C. `spark.sql.autoBroadcastJoinThreshold` only  
D. `delta.appendOnly`

**Question 27**  
**Objective:** Understand `spark.sql.shuffle.partitions` and re-measure performance  
A data engineer changes `spark.sql.shuffle.partitions` from 200 to 800. What should they do next?

A. Re-run and compare execution metrics such as duration, task count, shuffle, and spill.  
B. Assume performance is better because the number is larger.  
C. Delete the target Delta table history.  
D. Disable Unity Catalog.

**Question 28**  
**Objective:** Understand `spark.default.parallelism`  
Which statement best describes `spark.default.parallelism`?

A. It affects default task parallelism for lower-level RDD-style operations and some operations needing a default task count.  
B. It grants Unity Catalog privileges.  
C. It controls the number of columns in a DataFrame.  
D. It enables materialized view refreshes.

**Question 29**  
**Objective:** Understand `spark.executor.memory`  
Executors are failing with out-of-memory errors during wide transformations. Which setting may be relevant if the workload genuinely needs more memory per executor?

A. `spark.executor.memory`  
B. `spark.driver.host` only  
C. `spark.sql.autoBroadcastJoinThreshold = -1` always  
D. `CREATE VIEW`

**Question 30**  
**Objective:** Understand `spark.driver.memory`  
A notebook calls `collect()` on a large DataFrame and the driver runs out of memory. What is the best first correction?

A. Avoid collecting large datasets to the driver; process data in Spark and only collect small results.  
B. Broadcast the driver to all executors.  
C. Use a cross join to reduce driver memory.  
D. Convert the Delta table to a regular view.

**Question 31**  
**Objective:** Understand `spark.sql.autoBroadcastJoinThreshold`  
A small dimension table is below the automatic broadcast threshold. What can Spark do during a join?

A. Broadcast the small table to avoid shuffling the large side.  
B. Broadcast the large fact table by default.  
C. Convert the join into a union.  
D. Disable the query plan.

**Question 32**  
**Objective:** Understand `spark.sql.autoBroadcastJoinThreshold`  
A data engineer sets `spark.sql.autoBroadcastJoinThreshold` to `-1`. What is the effect?

A. It disables automatic broadcast joins.  
B. It forces every table to broadcast.  
C. It drops all duplicate records.  
D. It increases driver memory to unlimited.

**Question 33**  
**Objective:** Understand the difference between Gold layer objects  
A BI team repeatedly queries an expensive daily aggregation. The result should be precomputed for faster dashboard performance. Which Gold object is the best fit?

A. Materialized view  
B. Regular view  
C. Bronze raw table  
D. Cross join

**Question 34**  
**Objective:** Understand the difference between Gold layer objects  
A team wants a lightweight semantic layer that always reflects current silver tables and does not store query results. Which object is most appropriate?

A. View  
B. Materialized view  
C. Streaming table  
D. External location

**Question 35**  
**Objective:** Understand the difference between Gold layer objects  
A pipeline processes newly arriving event rows incrementally and writes the result as a managed object in Unity Catalog. Which object type is most aligned?

A. Streaming table  
B. Notebook markdown file  
C. Static CSV upload  
D. SQL query comment

**Question 36**  
**Objective:** Build Gold tables for BI and analytics teams  
A gold table should store `order_date`, `revenue`, and `order_count` for dashboard queries. Which SQL pattern is correct?

A. `CREATE OR REPLACE TABLE gold.daily_sales AS SELECT order_date, sum(amount) AS revenue, count(*) AS order_count FROM silver.orders GROUP BY order_date`  
B. `CREATE OR REPLACE TABLE gold.daily_sales AS SELECT * FROM bronze.orders_raw`  
C. `DROP TABLE gold.daily_sales GROUP BY order_date`  
D. `CREATE VIEW gold.daily_sales AS CROSS JOIN silver.orders`

**Question 37**  
**Objective:** Build Gold layer materialized views  
A data engineer wants to create or refresh a materialized view for daily revenue. Which syntax is most appropriate?

A. `CREATE OR REFRESH MATERIALIZED VIEW catalog.schema.mv_daily_sales AS SELECT ... GROUP BY ...`  
B. `CREATE STREAMING TABLE MATERIALIZED AS DROP ...`  
C. `COPY INTO MATERIALIZED VIEW FROM JDBC`  
D. `OPTIMIZE VIEW AS SELECT ...`

**Question 38**  
**Objective:** Build Gold layer views  
A data engineer wants to expose only active customers to analysts without physically storing another table. Which SQL object should be created?

A. `CREATE OR REPLACE VIEW gold.vw_active_customers AS SELECT ... WHERE is_active = true`  
B. `CREATE OR REPLACE TABLE bronze.customers_raw AS SELECT ...`  
C. `CREATE OR REFRESH STREAMING TABLE gold.vw_active_customers AS CROSS JOIN ...`  
D. `CREATE CONNECTION gold.vw_active_customers AS SELECT ...`

**Question 39**  
**Objective:** Build Gold layer streaming tables  
A Lakeflow Declarative Pipeline should read from a streaming bronze table and create a clean incremental silver table. Which SQL pattern is most appropriate?

A. `CREATE OR REFRESH STREAMING TABLE silver.events_clean AS SELECT ... FROM STREAM(bronze.events_raw)`  
B. `CREATE VIEW silver.events_clean AS SELECT ... FROM STREAMING WAREHOUSE`  
C. `DROP STREAM(bronze.events_raw)`  
D. `COPY INTO STREAMING TABLE FROM REST API`

**Question 40**  
**Objective:** Apply data quality checks and validation rules  
A silver dataset must not publish rows with null `order_id`; invalid rows can be dropped and counted as data quality metrics. Which Lakeflow expectation action fits?

A. `EXPECT (order_id IS NOT NULL) ON VIOLATION DROP ROW`  
B. `EXPECT (order_id IS NULL) ON VIOLATION KEEP ONLY BAD ROWS`  
C. `BROADCAST(order_id)`  
D. `UNION ALL order_id`

**Question 41**  
**Objective:** Apply data quality checks and validation rules  
A data-quality failure should stop the pipeline because publishing bad data would be unsafe. Which expectation action is most appropriate?

A. Fail the update on violation.  
B. Always retain invalid records silently.  
C. Convert invalid records into a cross join.  
D. Increase `spark.default.parallelism`.

**Question 42**  
**Objective:** Apply data quality checks and validation rules  
A team wants to preserve invalid source rows for investigation while publishing only valid silver rows. Which design is best?

A. Write valid rows to silver and invalid rows to a quarantine table.  
B. Delete invalid rows without recording them.  
C. Publish all invalid rows to gold dashboards.  
D. Disable all filters and constraints.

**Question 43**  
**Objective:** Apply data quality checks and validation rules  
A Lakeflow Python pipeline uses decorators for data quality. Which decorator name indicates invalid rows should be dropped?

A. `@dp.expect_or_drop`  
B. `@dp.broadcast_or_drop`  
C. `@dp.union_or_drop`  
D. `@dp.shuffle_or_drop`

**Question 44**  
**Objective:** Apply data quality checks and validation rules  
Which rule is most appropriate for validating silver order data?

A. `order_id IS NOT NULL AND amount >= 0`  
B. `spark.driver.memory = 8g`  
C. `customer_id CROSS JOIN amount`  
D. `DROP TABLE bronze.orders_raw`

**Question 45**  
**Objective:** Combine DataFrames and apply data quality  
A data engineer joins orders to customers and notices duplicate rows because the customer table has multiple rows per `customer_id`. What should they do before the join?

A. Deduplicate customers at the correct grain, such as keeping the latest row per `customer_id`.  
B. Cross join orders and customers to remove duplicates.  
C. Drop `customer_id` from both DataFrames.  
D. Increase driver memory and keep the duplicate logic.

**Question 46**  
**Objective:** Manipulate columns and rows  
A nested events table contains an array of item structs. The target table should create one row per item while retaining the event ID. Which pattern is correct?

A. `df.select("event_id", explode("items").alias("item"))`  
B. `df.drop("items").select("event_id")`  
C. `df.groupBy("items").mean("event_id")`  
D. `df.unionByName("items")`

**Question 47**  
**Objective:** Perform deduplication and aggregation  
A table has exact duplicate rows from a repeated load. The business wants full-row duplicate removal before aggregation. Which operation should be used first?

A. `dropDuplicates()`  
B. `crossJoin()`  
C. `broadcast()`  
D. `summary()`

**Question 48**  
**Objective:** Understand Gold layer object selection  
A dimensional lookup changes occasionally, and dashboard results must reflect historical fact rows joined to the latest dimension values after refresh. Which object is usually better than a streaming table for recomputing the result?

A. Materialized view  
B. Streaming table that processes each input row once  
C. Raw bronze table  
D. Local CSV upload

**Question 49**  
**Objective:** Understand basic tuning and re-measure performance  
A query has one task running far longer than others after a join, with high shuffle read. What performance issue is likely?

A. Data skew  
B. Null filling  
C. Column rename  
D. SQL view definition only

**Question 50**  
**Objective:** Data Transformation and Modeling integrated scenario  
A data engineer must build a reliable BI dataset from raw orders. Requirements: remove rows without `order_id`, cast `amount`, keep the latest row per `order_id`, enrich with a small product lookup, aggregate revenue by day and category, and publish a precomputed Gold object. Which sequence is best?

A. Clean bronze to silver, dedupe using a window by `order_id`, broadcast join the small product table, aggregate, and create a materialized view or gold table.  
B. Cross join all raw orders to products, collect to the driver, then write notebook output to markdown.  
C. Rename bronze to gold, skip dedupe, and use a regular view for every expensive dashboard query.  
D. Increase driver memory, leave negative and null rows unchanged, and create a local CSV.

## Answer Key

**Question 1: A**  
A is correct because it reads bronze, filters invalid rows, casts `amount`, and writes a silver table. B reverses the direction and filters null IDs. C removes the key required for the silver table. D deletes source data and performs no transformation.

**Question 2: A**  
A is correct because `fillna` replaces null values with a default. B removes the column. C keeps only null rows rather than fixing them. D renames the column instead of replacing nulls.

**Question 3: A**  
A is correct because `to_timestamp` converts a string to a timestamp. B explodes arrays. C is a join optimization. D computes a distinct estimate.

**Question 4: A**  
A is correct because `WHERE` filters rows and the cast standardizes the value before comparison. B, C, and D use grouping, joining, or ordering instead of row filtering.

**Question 5: A**  
A is correct because bronze should preserve raw or lightly parsed data while silver stores cleaned data. B loses raw source lineage. C exposes unvalidated data to BI. D skips the intended cleaning/modeling layers.

**Question 6: A**  
A is correct because an inner join keeps only matching rows. B preserves all left-side rows, including unmatched orders. C creates all combinations. D stacks rows and does not match customers.

**Question 7: A**  
A is correct because a left join preserves all order rows while adding product fields when available. B drops orders without product matches. C creates a Cartesian product. D stacks tables rather than enriching rows.

**Question 8: A**  
A is correct because broadcasting the small lookup avoids shuffling the large fact table. B broadcasts the huge table and can fail or harm performance. C creates an explosive Cartesian product. D appends rows instead of joining.

**Question 9: A**  
A is correct because passing a list of key column names joins on both keys. B treats the string incorrectly as a single column name. C ignores keys. D appends rows rather than matching rows.

**Question 10: A**  
A is correct because a cross join intentionally creates every combination of calendar date and region. B requires a matching key and would not create all combinations. C stacks rows. D deduplicates, not grid creation.

**Question 11: A**  
A is correct because PySpark `unionByName` keeps all rows, including duplicates. B joins rows by key. C creates combinations. D removes duplicate keys.

**Question 12: A**  
A is correct because `unionByName` aligns columns by name. B can misalign values if column order differs. C and D perform different operations.

**Question 13: A**  
A is correct because `withColumn` adds or replaces a column using an expression. B removes columns. C filters rows. D aggregates by a column rather than creating one.

**Question 14: A**  
A is correct because `drop` removes specified columns. B filters rows based on one column. C creates or replaces a column incorrectly. D aggregates by those columns.

**Question 15: A**  
A is correct because `withColumnRenamed` changes a column name. B removes a different column. C filters rows. D explodes arrays.

**Question 16: A**  
A is correct because `split` tokenizes the email string and `getItem(1)` selects the domain portion. B is for arrays and is not appropriate for splitting a string. C and D are unrelated.

**Question 17: A**  
A is correct because it applies both predicates with a boolean AND. B groups by a boolean expression. C and D are invalid transformation choices for filtering rows.

**Question 18: A**  
A is correct because `explode` converts array elements into separate rows. B splits strings. C optimizes joins. D aggregates numeric columns.

**Question 19: A**  
A is correct because `ALTER TABLE ... RENAME COLUMN` is the SQL table-structure command. B, C, and D are not valid column rename patterns.

**Question 20: A**  
A is correct because `dropDuplicates(["order_id"])` leaves one row per key when any row is acceptable. B removes the key. C returns a count, not deduped rows. D is not a valid dedupe operation.

**Question 21: A**  
A is correct because a window with `row_number` can deterministically select the latest record. B is wrong because `dropDuplicates` does not guarantee the latest. C loses row-level data. D creates a Cartesian product.

**Question 22: A**  
A is correct because `groupBy` followed by `agg` computes daily counts and sums. B, C, and D misuse filtering, column creation, or union.

**Question 23: A**  
A is correct because `approx_count_distinct` estimates cardinality efficiently for large datasets. B computes a mean, C explodes arrays, and D is a join hint.

**Question 24: A**  
A is correct because `summary` returns descriptive statistics. B, C, and D are unrelated.

**Question 25: A**  
A is correct because `count("*")` counts all rows, while `count("customer_id")` ignores nulls in that column. B reverses behavior. C describes distinct counts, not regular count. D is false.

**Question 26: A**  
A is correct because `spark.sql.shuffle.partitions` controls default shuffle partitions for SQL/DataFrame joins and aggregations. B may help driver memory but not shuffle partition count. C affects broadcast joins. D is unrelated.

**Question 27: A**  
A is correct because tuning requires measuring the new run against baseline metrics. B is blind tuning. C and D are unrelated and harmful.

**Question 28: A**  
A is correct because `spark.default.parallelism` affects default task parallelism, especially for lower-level RDD operations. B, C, and D are unrelated.

**Question 29: A**  
A is correct because executor memory affects memory available to Spark executors. B concerns driver networking. C disables automatic broadcast and is not a general OOM fix. D creates a view.

**Question 30: A**  
A is correct because large `collect()` calls bring data to the driver and can cause driver OOM. B and C are invalid concepts. D does not solve driver collection pressure.

**Question 31: A**  
A is correct because Spark can broadcast a small table below the threshold to avoid shuffling the large side. B is not the intended behavior. C and D are false.

**Question 32: A**  
A is correct because `-1` disables automatic broadcast joins. B is the opposite. C and D are unrelated.

**Question 33: A**  
A is correct because materialized views store precomputed results and can improve repeated dashboard queries. B recomputes each time. C is raw source data. D is a join operation.

**Question 34: A**  
A is correct because a regular view stores a query definition and reflects current source data. B stores precomputed results. C is for streaming/incremental processing. D governs storage paths.

**Question 35: A**  
A is correct because streaming tables are aligned with incremental processing of arriving rows. B, C, and D are not managed data objects for streaming transformations.

**Question 36: A**  
A is correct because it creates a gold aggregate table grouped by order date. B copies raw data without modeling. C is invalid syntax. D incorrectly uses a cross join for daily metrics.

**Question 37: A**  
A is correct because `CREATE OR REFRESH MATERIALIZED VIEW ... AS SELECT` is the relevant pattern. B, C, and D are invalid or unrelated.

**Question 38: A**  
A is correct because a view exposes query logic without physically storing another table. B creates a table. C creates a streaming table and is not needed. D creates a connection, not a data object.

**Question 39: A**  
A is correct because `CREATE OR REFRESH STREAMING TABLE ... FROM STREAM(...)` is the intended pattern for streaming table transformations. B, C, and D are invalid patterns.

**Question 40: A**  
A is correct because an expectation with `ON VIOLATION DROP ROW` drops bad rows and records metrics. B keeps the wrong rows. C and D are unrelated.

**Question 41: A**  
A is correct because failing the update prevents unsafe publication. B silently allows bad data. C and D do not enforce quality.

**Question 42: A**  
A is correct because quarantine preserves invalid records while keeping silver clean. B loses evidence. C publishes bad data. D removes validation.

**Question 43: A**  
A is correct because `@dp.expect_or_drop` is the Python decorator pattern for dropping invalid rows in Lakeflow Declarative Pipelines. B, C, and D are invented decorators.

**Question 44: A**  
A is correct because it validates required identifiers and non-negative amounts. B is a tuning setting. C is not a validation rule. D deletes source data.

**Question 45: A**  
A is correct because dimension duplicates should be resolved at the right grain before joining to avoid duplicate fact rows. B makes duplicates worse. C removes join keys. D masks the modeling issue.

**Question 46: A**  
A is correct because `explode` creates one row per array element while keeping selected parent columns. B loses item detail. C and D are unrelated.

**Question 47: A**  
A is correct because `dropDuplicates()` removes full-row duplicates. B, C, and D do not deduplicate rows.

**Question 48: A**  
A is correct because materialized views can recompute precomputed results after refresh, which fits changing dimension joins better than append-style streaming rows. B may not recompute prior rows automatically. C and D are not curated gold choices.

**Question 49: A**  
A is correct because one long-running task with high shuffle is a classic data skew symptom. B, C, and D are unrelated to uneven task duration after joins.

**Question 50: A**  
A is correct because it follows the intended bronze-to-silver-to-gold flow, uses deterministic dedupe, broadcasts the small lookup, aggregates for BI, and publishes a precomputed object. B is inefficient and unsafe. C skips required quality and dedupe. D increases memory without fixing data correctness.

