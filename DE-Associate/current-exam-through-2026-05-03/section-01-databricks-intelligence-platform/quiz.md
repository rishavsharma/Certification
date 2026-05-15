# Section 1 Quiz (Current Exam Through May 3, 2026)

## Multiple Choice Questions (50)

1. A Delta table supports a wide range of ad hoc filter patterns, and the team keeps debating which columns to partition by and which columns to `ZORDER` on. Which Databricks feature is most aligned with simplifying these layout decisions while improving query performance?
   A. Hive-style partitioning with daily partitions
   B. Liquid clustering (optionally with automatic key selection)
   C. Turning off Delta statistics collection
   D. Using `VACUUM` more frequently

2. A data engineer runs `ALTER TABLE sales CLUSTER BY (customer_id, order_date)` on an existing table. Existing data files remain laid out by the old strategy. Which action is most directly required to reorganize existing data according to the new clustering keys?
   A. Run `VACUUM sales`
   B. Run `OPTIMIZE sales`
   C. Run `ANALYZE TABLE sales COMPUTE STATISTICS`
   D. Restart the compute used to query the table

3. Which statement best describes automatic liquid clustering using `CLUSTER BY AUTO`?
   A. It disables file compaction to preserve ingest ordering
   B. It forces partitioning by the first column in the schema
   C. Databricks automatically selects and adapts clustering keys based on query patterns
   D. It automatically adds `ZORDER BY` to every query

4. Which feature is primarily intended to automatically run maintenance operations like `OPTIMIZE` and `VACUUM` for Unity Catalog managed tables?
   A. Predictive optimization
   B. Photon
   C. Compute policies
   D. SQL warehouse sizing

5. A Delta table receives frequent small batch writes and now has thousands of tiny data files. Which pair of features most directly targets reducing the small-file problem during and after writes?
   A. `VACUUM` and `RESTORE`
   B. Optimized writes and auto compaction
   C. `DESCRIBE HISTORY` and `DESCRIBE DETAIL`
   D. `ZORDER` and `CACHE TABLE`

6. A team believes "Auto Optimize" is a single setting they must enable to improve Delta file sizing. Which statement best reflects current Databricks documentation?
   A. "Auto Optimize" is the preferred term, replacing optimized writes and auto compaction
   B. "Auto Optimize" is a retired umbrella term; docs describe optimized writes and auto compaction separately
   C. "Auto Optimize" is only available for Apache Iceberg tables
   D. "Auto Optimize" is required for liquid clustering to work

7. Which statement is true about `OPTIMIZE` on a table with liquid clustering enabled?
   A. `OPTIMIZE` rewrites files to group data by liquid clustering keys
   B. `OPTIMIZE` can only compact files inside Hive partitions and cannot affect clustering
   C. `OPTIMIZE` changes query results by filtering out deleted rows
   D. `OPTIMIZE` is a metadata-only operation that never rewrites data files

8. Which statement best matches current Databricks recommendations for modern Delta table layout tuning?
   A. Use partitions and `ZORDER` for all tables; clustering is legacy
   B. Avoid `OPTIMIZE`; rely solely on `VACUUM`
   C. Prefer liquid clustering instead of partitions, `ZORDER`, or other manual layout approaches for most tables
   D. Disable statistics collection to reduce metadata overhead

9. A team changes clustering keys on a Delta table, but queries show no performance improvement. What is the most likely explanation?
   A. Clustering keys only affect `VACUUM`, not reads
   B. Clustering keys only apply to new data until an `OPTIMIZE` rewrites existing files
   C. Clustering keys require disabling Photon
   D. Clustering keys are ignored by SQL warehouses

10. Which command is used to *set* or *change* liquid clustering keys on an existing Delta table?
   A. `ALTER TABLE t CLUSTER BY (...)`
   B. `ALTER TABLE t PARTITION BY (...)`
   C. `ALTER TABLE t ZORDER BY (...)`
   D. `ALTER TABLE t SORT BY (...)`

11. A table owner wants Databricks to stop applying liquid clustering going forward, without rewriting existing data immediately. Which option most directly matches that goal?
   A. `ALTER TABLE t CLUSTER BY NONE`
   B. `ALTER TABLE t CLUSTER BY AUTO`
   C. `VACUUM t RETAIN 0 HOURS`
   D. `CACHE TABLE t`

12. Predictive optimization is described in Databricks docs as being supported for which table type?
   A. Any external table in object storage
   B. Unity Catalog managed tables
   C. All Hive metastore tables
   D. Views only

13. Which statement about optimized writes is most accurate?
   A. It is a read-time cache that keeps Parquet files in memory
   B. It improves file size as data is written, often reducing the need for manual `repartition`/`coalesce` before writing
   C. It permanently deletes old versions of Delta files
   D. It only applies to `VACUUM` operations

14. Which statement about auto compaction is most accurate?
   A. It converts Delta tables to Parquet-only tables
   B. It combines small files to reduce the small-file problem after writes
   C. It is required to use Unity Catalog
   D. It automatically `ZORDER`s all tables

15. A team enables liquid clustering but still plans to run `OPTIMIZE` occasionally. Which statement best aligns with Databricks behavior?
   A. `OPTIMIZE` is irrelevant once clustering is enabled
   B. `OPTIMIZE` is the operation that rewrites files to apply clustering to existing data
   C. `OPTIMIZE` disables clustering by default
   D. `OPTIMIZE` changes clustering keys automatically

16. For a large Delta table where the workload is unknown and changes over time, which approach is most aligned with "simplify data layout decisions"?
   A. Hard-code partitions by every frequently used column
   B. Use `CLUSTER BY AUTO` and predictive optimization
   C. Disable `OPTIMIZE` to avoid file rewrites
   D. Use only `VACUUM` and never rewrite files

17. Which statement best describes the relationship between optimized writes/auto compaction and `OPTIMIZE`?
   A. Optimized writes and auto compaction fully replace the need for `OPTIMIZE` in all cases
   B. Optimized writes and auto compaction help reduce small files, but do not fully replace `OPTIMIZE`
   C. `OPTIMIZE` only affects streaming tables, while optimized writes affects batch tables
   D. `OPTIMIZE` only applies to Iceberg tables, not Delta tables

18. A data engineer wants to view clustering configuration details for a table using SQL. Which commands are commonly used for this purpose?
   A. `DESCRIBE DETAIL t` and `SHOW TBLPROPERTIES t`
   B. `SHOW VIEWS` and `SHOW FUNCTIONS`
   C. `REFRESH TABLE t` and `CACHE TABLE t`
   D. `TRUNCATE TABLE t` and `ANALYZE TABLE t`

19. Which statement about `ZORDER` is most consistent with current Databricks docs?
   A. `ZORDER` is automatically run by predictive optimization on all tables
   B. `ZORDER` is a Delta Lake-specific optimization; Databricks commonly recommends liquid clustering instead of relying on `ZORDER`
   C. `ZORDER` is required for Unity Catalog
   D. `ZORDER` is an Apache Iceberg-only feature

20. A team wants "set-and-forget" table maintenance (compaction, layout rewrite, and cleanup) for managed tables. Which feature most directly matches this goal?
   A. Predictive optimization
   B. Creating more partitions
   C. Disabling Delta statistics
   D. Turning off optimized writes

21. In Databricks Runtime 10.4 LTS and above, which statement is accurate for many Delta DML operations?
   A. Optimized writes and auto compaction are always disabled for `MERGE`, `UPDATE`, and `DELETE`
   B. Optimized writes and auto compaction are always enabled for `MERGE`, `UPDATE`, and `DELETE` and cannot be disabled for those operations
   C. `OPTIMIZE` is automatically run after every `SELECT`
   D. `VACUUM` is automatically run after every write

22. Which of the following is the best high-level description of why liquid clustering improves query performance?
   A. It stores all data in a single file to avoid metadata
   B. It clusters rows by selected keys to improve data skipping for common filter patterns
   C. It disables the Delta transaction log to reduce overhead
   D. It forces all queries to use broadcast joins

23. A data engineer needs to run BI dashboards with high concurrency and rapidly changing query demand during business hours. Which compute is the best default choice?
   A. Classic all-purpose compute
   B. Serverless SQL warehouse
   C. Classic jobs compute
   D. Single-node classic compute

24. A team has a scheduled ETL job that runs nightly and does not require special cluster configuration. Which compute choice is most aligned with Databricks recommendations?
   A. Serverless compute for jobs
   B. Classic all-purpose compute
   C. A classic SQL warehouse (classic type)
   D. An interactive notebook attached to a pro SQL warehouse

25. A user needs to run R language code in a notebook. Which compute type is the most appropriate choice based on Databricks guidance?
   A. Serverless compute for notebooks
   B. Classic all-purpose compute
   C. Serverless SQL warehouse
   D. Predictive optimization compute

26. A team must run SQL workloads but serverless SQL warehouses are not available in their region, and they also require custom networking to connect to databases in their private network for federation. Which SQL warehouse type best fits?
   A. Serverless SQL warehouse
   B. Pro SQL warehouse
   C. Classic SQL warehouse
   D. Classic jobs compute

27. Which SQL warehouse type supports Intelligent Workload Management (IWM) according to Databricks docs?
   A. Classic
   B. Pro
   C. Serverless
   D. None of them

28. Which statement is most accurate about where compute runs for serverless versus classic compute?
   A. Both run only in the Databricks control plane
   B. Serverless compute runs in a Databricks-managed serverless compute plane; classic compute runs in the customer cloud account
   C. Serverless compute runs only on the user's local machine; classic compute runs in Databricks-managed infrastructure
   D. Both run only in the customer cloud account

29. A Lakeflow declarative pipeline is being configured for a common ingestion-and-transform use case with no special constraints. Which compute is the best default choice?
   A. Classic pipeline compute
   B. Serverless pipelines
   C. Classic all-purpose compute
   D. Classic SQL warehouse

30. A job task is purely SQL: it runs a SQL statement that creates a reporting table and then refreshes dashboards. Which compute option is most directly aligned?
   A. SQL warehouse
   B. Classic all-purpose compute
   C. Dedicated classic all-purpose compute without Unity Catalog
   D. A single-node classic compute

31. A team is choosing compute for interactive notebooks. Which statement best matches Databricks documentation?
   A. Notebooks can only run on classic all-purpose compute
   B. Notebooks can run on serverless compute, classic all-purpose compute, or (for SQL commands) a SQL warehouse
   C. Notebooks can only run on SQL warehouses
   D. Notebooks must always use classic jobs compute

32. A data engineer is deciding between "classic jobs compute" and "classic all-purpose compute" for a scheduled job. Which statement best reflects Databricks guidance?
   A. Classic all-purpose compute is generally recommended for automated workloads
   B. Classic jobs compute is intended for jobs; classic all-purpose compute is generally avoided for automated workloads
   C. Only classic all-purpose compute supports Spark jobs
   D. Only classic all-purpose compute supports autoscaling

33. Which compute choice is most appropriate for a workload that is primarily SQL analytics and reporting?
   A. SQL warehouse
   B. Classic jobs compute
   C. Classic all-purpose compute (shared)
   D. Serverless compute for jobs

34. A Spark ETL job relies on custom cluster settings not available in serverless compute. Which compute type is the best fit?
   A. Serverless compute for jobs
   B. Classic jobs compute
   C. Serverless SQL warehouse
   D. Classic SQL warehouse

35. Which statement about serverless compute is most consistent with Databricks docs?
   A. Serverless compute is versionless and Databricks automatically upgrades the runtime
   B. Serverless compute requires manual patching of the runtime by workspace admins
   C. Serverless compute always runs inside the customer cloud account
   D. Serverless compute can only be used for interactive notebooks

36. Which SQL warehouse type supports Photon, Predictive IO, and Intelligent Workload Management according to Databricks docs?
   A. Classic
   B. Pro
   C. Serverless
   D. All types support all three

37. Which SQL warehouse type supports Photon but does not support Predictive IO or Intelligent Workload Management?
   A. Serverless
   B. Pro
   C. Classic
   D. None of them

38. A question asks you to choose compute for "SQL-based ETL" (SQL transformations scheduled as a job). Which compute option is most aligned with Databricks guidance?
   A. SQL warehouse
   B. Classic all-purpose compute
   C. Classic pipeline compute only
   D. Single-node classic compute only

39. Which statement best captures the tradeoff of serverless compute versus classic compute for many workloads?
   A. Serverless increases operational overhead but reduces performance
   B. Serverless reduces configuration and management effort with fast startup and scaling; classic provides more customization
   C. Serverless can only run SQL; classic can only run Python
   D. Serverless is always cheaper in all cases, regardless of workload

40. Which compute selection is the best match for "basic interactive SQL exploration" when serverless is not an option?
   A. Classic SQL warehouse
   B. Serverless pipelines
   C. Classic jobs compute
   D. Serverless compute for jobs

41. A data engineer needs to run a high-throughput Spark job with Python transformations (not just SQL) on a schedule. Which is the best default compute choice when supported?
   A. Serverless compute for jobs
   B. Serverless SQL warehouse
   C. Classic SQL warehouse
   D. SQL editor in Databricks SQL (no compute)

42. Which compute option is most directly associated with Databricks SQL dashboards and the SQL editor?
   A. SQL warehouse
   B. Classic all-purpose compute
   C. Classic pipeline compute
   D. Serverless compute for jobs

43. Which statement best describes a data lakehouse?
   A. A system that stores data only in proprietary formats with a single compute engine
   B. A data management system combining data lake scale and open storage with warehouse-style performance and governance features
   C. A transactional database designed only for OLTP workloads
   D. A BI tool that replaces SQL engines

44. In Databricks lakehouse architecture, which two technologies are commonly highlighted as foundational for tables and governance?
   A. Delta Lake and Unity Catalog
   B. Hive metastore and MySQL
   C. CSV and spreadsheet macros
   D. Local disk and Python pickles

45. Which statement best describes the separation between control plane and compute plane in Databricks architecture?
   A. The control plane stores all customer data files; the compute plane stores metadata only
   B. The control plane hosts the workspace application and backend services; the compute plane runs workloads that process data
   C. The compute plane is only used for UI rendering; the control plane runs Spark jobs
   D. There is no separation; all workloads run in a single plane

46. Unity Catalog organizes data objects using which namespace pattern?
   A. `<workspace>.<cluster>.<table>`
   B. `<catalog>.<schema>.<object>`
   C. `<database>.<file>.<row>`
   D. `<region>.<account>.<user>`

47. Which best describes Delta Lake's core storage concept?
   A. Parquet files plus a file-based transaction log enabling ACID transactions and scalable metadata
   B. JSON files plus a relational database catalog only
   C. A proprietary binary format accessible only via Databricks SQL
   D. In-memory tables that disappear when compute stops

48. What is Photon in Databricks?
   A. A governance layer that manages permissions across workspaces
   B. A high-performance vectorized query engine that accelerates SQL and DataFrame workloads
   C. A file compaction tool that replaces `OPTIMIZE`
   D. A connector that ingests data from SaaS applications

49. Databricks SQL runs primarily on which compute resource?
   A. Classic jobs compute
   B. SQL warehouses
   C. Dedicated clusters only
   D. Local mode Spark

50. Which is the best "platform value" explanation for why organizations adopt the Databricks Data Intelligence Platform?
   A. It forces all teams to use separate copies of data for each workload
   B. It unifies multiple workload types (engineering, analytics, AI) on open storage with centralized governance and reduced data movement
   C. It replaces object storage with a single-machine database
   D. It removes the need for SQL entirely
