# Section 3 Quiz Answers (52 MCQs)

1. A. Objective: Describe the three layers of the Medallion Architecture and explain the purpose of each layer in a data processing pipeline. Rationale: Bronze is for raw ingestion with minimal cleanup and supports replay/audit.

2. C. Objective: Describe the three layers of the Medallion Architecture and explain the purpose of each layer in a data processing pipeline. Rationale: Gold is for curated, business-facing datasets (KPIs, aggregates) optimized for analytics consumption.

3. C. Objective: Identify DDL (Data Definition Language)/DML features. Rationale: `CREATE OR REPLACE TABLE` creates the table if missing and replaces it if it already exists.

4. D. Objective: Identify DDL (Data Definition Language)/DML features. Rationale: The correct append syntax is `INSERT INTO <table> VALUES (...)`.

5. A. Objective: Identify DDL (Data Definition Language)/DML features. Rationale: `MERGE INTO` is the Delta SQL operation designed for upserts (update matched rows and insert non-matched rows).

6. B. Objective: Identify DDL (Data Definition Language)/DML features. Rationale: `DELETE FROM <table> WHERE <predicate>` removes only rows matching the predicate.

7. C. Objective: Compute complex aggregations and Metrics with PySpark Dataframes. Rationale: Daily invoices requires a distinct count of invoice identifiers, which is `count_distinct("billing_id")`.

8. B. Objective: Compute complex aggregations and Metrics with PySpark Dataframes. Rationale: `count(col)` counts only non-NULL values, while `count("*")` counts rows.

9. B. Objective: Compute complex aggregations and Metrics with PySpark Dataframes. Rationale: `approx_count_distinct` is intended for approximate distinct counts when exact counts are too expensive.

10. B. Objective: Compute complex aggregations and Metrics with PySpark Dataframes. Rationale: Providing pivot values avoids the eager computation of distinct pivot values.

11. B. Objective: Compute complex aggregations and Metrics with PySpark Dataframes. Rationale: Subtotal/grand-total rows represent aggregated levels, so dimensions not present at that level are typically NULL.

12. B. Objective: Compute complex aggregations and Metrics with PySpark Dataframes. Rationale: `rowsBetween` defines a row-based frame; 7 rows is not equivalent to 7 days when there are multiple rows per day.

13. B. Objective: Compute complex aggregations and Metrics with PySpark Dataframes. Rationale: `GroupedData.agg(...)` is designed to accept multiple aggregate expressions (sum, max, distinct count, etc.).

14. B. Objective: Compute complex aggregations and Metrics with PySpark Dataframes. Rationale: `collect_set` does not guarantee ordering; sorting the resulting array makes downstream comparisons deterministic.

15. A. Objective: Compute complex aggregations and Metrics with PySpark Dataframes. Rationale: `count_distinct` supports counting distinct combinations across multiple columns.

16. B. Objective: Compute complex aggregations and Metrics with PySpark Dataframes. Rationale: `count("*")` counts all rows in the group regardless of NULLs in any particular column.

17. B. Objective: Compute complex aggregations and Metrics with PySpark Dataframes. Rationale: The standard pattern is `groupBy(...).pivot(...).sum(...)`.

18. A. Objective: Compute complex aggregations and Metrics with PySpark Dataframes. Rationale: `DataFrame.groupingSets(...)` is the PySpark API designed for multi-dimensional aggregation with subtotals.

19. A. Objective: Classify the type of cluster and configuration for optimal performance based on the scenario in which the cluster is used. Rationale: For notebook job tasks, docs recommend serverless jobs when supported to minimize infrastructure management.

20. A. Objective: Classify the type of cluster and configuration for optimal performance based on the scenario in which the cluster is used. Rationale: For SQL tasks in Jobs, the recommended compute is a serverless SQL warehouse.

21. B. Objective: Classify the type of cluster and configuration for optimal performance based on the scenario in which the cluster is used. Rationale: Spark Submit tasks are recommended on classic jobs compute (serverless jobs is not the recommended fit here).

22. B. Objective: Classify the type of cluster and configuration for optimal performance based on the scenario in which the cluster is used. Rationale: For an LDP task in Jobs, the recommended compute is a serverless pipeline.

23. C. Objective: Classify the type of cluster and configuration for optimal performance based on the scenario in which the cluster is used. Rationale: All-purpose compute is designed for interactive collaboration in notebooks.

24. C. Objective: Classify the type of cluster and configuration for optimal performance based on the scenario in which the cluster is used. Rationale: Databricks guidance for Jobs is to prefer jobs compute; all-purpose compute is not recommended as the default for production jobs.

25. A. Objective: Classify the type of cluster and configuration for optimal performance based on the scenario in which the cluster is used. Rationale: Standard access mode is recommended for most workloads and supports multi-user concurrent execution with isolation.

26. B. Objective: Classify the type of cluster and configuration for optimal performance based on the scenario in which the cluster is used. Rationale: Docs describe Auto defaulting to Standard in many cases, but selecting Dedicated for ML runtimes (or certain older runtimes).

27. C. Objective: Classify the type of cluster and configuration for optimal performance based on the scenario in which the cluster is used. Rationale: No isolation shared is a legacy access mode and is not recommended for Unity Catalog-enabled scenarios.

28. B. Objective: Classify the type of cluster and configuration for optimal performance based on the scenario in which the cluster is used. Rationale: Standard compute has language limitations (including R not supported), so a dedicated-style compute configuration is typically required.

29. B. Objective: Classify the type of cluster and configuration for optimal performance based on the scenario in which the cluster is used. Rationale: Serverless pipelines do not require classic compute creation permission; classic pipelines require compute permission/policies.

30. B. Objective: Classify the type of cluster and configuration for optimal performance based on the scenario in which the cluster is used. Rationale: Docs indicate classic warehouses have lower performance and slower responsiveness than pro or serverless options.

31. B. Objective: Classify the type of cluster and configuration for optimal performance based on the scenario in which the cluster is used. Rationale: Autoscaling adapts worker count to variable workload volume.

32. A. Objective: Classify the type of cluster and configuration for optimal performance based on the scenario in which the cluster is used. Rationale: Compute policies restrict configuration options and enforce standards/cost controls.

33. A. Objective: Emphasize the advantages of LDP (for ETL process in Databricks). Rationale: Databricks docs describe DLT as updated/renamed to Lakeflow Spark Declarative Pipelines.

34. B. Objective: Implement data pipelines using LDP. Rationale: Current docs recommend `from pyspark import pipelines as dp` as the Python API entry point.

35. B. Objective: Implement data pipelines using LDP. Rationale: Current docs use `@dp.temporary_view` to define pipeline views (the older `@view` was associated with legacy naming).

36. A. Objective: Implement data pipelines using LDP. Rationale: `dp.expect` retains invalid records (warn behavior) while tracking metrics.

37. B. Objective: Implement data pipelines using LDP. Rationale: `dp.expect_or_drop` drops invalid records before writing the target dataset.

38. C. Objective: Implement data pipelines using LDP. Rationale: `dp.expect_or_fail` fails the update when a record violates the expectation.

39. B. Objective: Implement data pipelines using LDP. Rationale: SQL expectations use `ON VIOLATION DROP ROW` to drop invalid records.

40. B. Objective: Emphasize the advantages of LDP (for ETL process in Databricks). Rationale: The `event_log()` table-valued function is designed to query LDP-related event logs for monitoring and troubleshooting.

41. C. Objective: Emphasize the advantages of LDP (for ETL process in Databricks). Rationale: Docs state `event_log()` access is owner-restricted and views over it cannot be shared for other users to query.

42. B. Objective: Implement data pipelines using LDP. Rationale: `LIVE` is documented as a legacy virtual schema for legacy publishing mode and is deprecated for new pipelines.

43. C. Objective: Implement data pipelines using LDP. Rationale: Docs describe the `LIVE` keyword as being ignored in the default publishing mode so unqualified identifiers resolve in the current schema.

44. B. Objective: Implement data pipelines using LDP. Rationale: Docs describe streaming tables as supported in LDP and in Databricks SQL with Unity Catalog.

45. B. Objective: Implement data pipelines using LDP. Rationale: The LDP SQL syntax uses `CREATE OR REFRESH STREAMING TABLE` (or `CREATE STREAMING TABLE`) to declare a streaming table.

46. B. Objective: Emphasize the advantages of LDP (for ETL process in Databricks). Rationale: Databricks SQL materialized views are updated by a backing pipeline created and managed by Databricks.

47. C. Objective: Emphasize the advantages of LDP (for ETL process in Databricks). Rationale: Docs state fail expectations stop the update; metrics are not recorded because the update fails when invalid records are detected.

48. B. Objective: Implement data pipelines using LDP. Rationale: Docs state failure of a single flow due to expectation violations does not automatically cause other flows to fail.

49. C. Objective: Implement data pipelines using LDP. Rationale: Docs explicitly allow mixing SQL and Python sources in LDP when some ingestion needs Python but transformations are easier in SQL.

50. A. Objective: Implement data pipelines using LDP. Rationale: Docs state serverless pipelines always use Unity Catalog and do not allow manually specifying `clusters` in JSON for serverless.

51. B. Objective: Implement data pipelines using LDP. Rationale: Docs state some compute attributes are not user-settable because pipeline compute lifecycle and runtime are managed by LDP.

52. A. Objective: Emphasize the advantages of LDP (for ETL process in Databricks). Rationale: The pipeline event log is described as the primary observability primitive, containing progress, data quality, lineage, and error details.
