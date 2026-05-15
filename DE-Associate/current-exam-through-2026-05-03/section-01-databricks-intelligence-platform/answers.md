# Section 1 Quiz Answers (Current Exam Through May 3, 2026)

Objectives (current blueprint wording):

1. Enable features that simplify data layout decisions and optimize query performance.
2. Explain the value of the Data Intelligence Platform.
3. Identify the applicable compute to use for a specific use case.

| Q# | Correct | Objective | Rationale |
|---:|:---:|---|---|
| 1 | B | 1 | Liquid clustering is positioned as the modern replacement for many partitioning and `ZORDER` decisions while improving skipping. |
| 2 | B | 1 | Changing clustering keys does not rewrite existing files; `OPTIMIZE` rewrites files to apply the new layout. |
| 3 | C | 1 | `CLUSTER BY AUTO` lets Databricks choose and adapt clustering keys based on observed query workload. |
| 4 | A | 1 | Predictive optimization automates maintenance like `OPTIMIZE` and `VACUUM` for supported managed tables. |
| 5 | B | 1 | Optimized writes reduces small files at write time; auto compaction compacts small files after writes. |
| 6 | B | 1 | Databricks docs treat "Auto Optimize" as retired terminology and describe optimized writes and auto compaction separately. |
| 7 | A | 1 | With liquid clustering enabled, `OPTIMIZE` rewrites files to group data by clustering keys. |
| 8 | C | 1 | Current guidance commonly recommends liquid clustering instead of partitions and `ZORDER` for most tables. |
| 9 | B | 1 | New clustering keys mostly affect new data until you run `OPTIMIZE` to rewrite existing files. |
| 10 | A | 1 | Liquid clustering keys are configured with `ALTER TABLE ... CLUSTER BY (...)`. |
| 11 | A | 1 | `CLUSTER BY NONE` disables clustering going forward without immediately rewriting existing data. |
| 12 | B | 1 | Predictive optimization is documented for Unity Catalog managed tables, not arbitrary external tables. |
| 13 | B | 1 | Optimized writes improves file sizing during writes and reduces the need for manual `coalesce`/`repartition` patterns. |
| 14 | B | 1 | Auto compaction combines small files to reduce small-file overhead after successful writes. |
| 15 | B | 1 | `OPTIMIZE` is how you apply clustering layout to existing data by rewriting files. |
| 16 | B | 1 | Automatic clustering plus predictive optimization is the most "hands-off" combination for evolving workloads. |
| 17 | B | 1 | These features mitigate small files but do not fully replace `OPTIMIZE` for deeper layout rewrites. |
| 18 | A | 1 | `DESCRIBE DETAIL` and `SHOW TBLPROPERTIES` are common SQL ways to inspect table layout-related properties. |
| 19 | B | 1 | Docs describe `ZORDER` as Delta-specific and often recommend liquid clustering instead for layout. |
| 20 | A | 1 | Predictive optimization is designed to automate maintenance decisions and execution for managed tables. |
| 21 | B | 1 | Databricks docs note these behaviors are always enabled for common DML in DBR 10.4 LTS+ and cannot be disabled for those operations. |
| 22 | B | 1 | Clustering physically groups rows by keys to improve file pruning and data skipping for common filters. |
| 23 | B | 3 | Serverless SQL warehouses are recommended for BI/dashboard workloads with variable demand and fast startup needs. |
| 24 | A | 3 | Serverless compute for jobs is the recommended default for automated workloads when no special constraints exist. |
| 25 | B | 3 | Classic all-purpose compute is the documented choice when you need R language support. |
| 26 | B | 3 | Pro warehouses are recommended when serverless isn't available and you need custom networking (for example, federation/hybrid). |
| 27 | C | 3 | Intelligent Workload Management is a serverless SQL warehouse capability. |
| 28 | B | 3 | Serverless runs in a Databricks-managed compute plane; classic compute runs in the customer cloud account. |
| 29 | B | 3 | Serverless pipelines are the recommended default for common pipeline workloads when supported. |
| 30 | A | 3 | SQL-only job tasks align directly with SQL warehouses as the SQL execution compute. |
| 31 | B | 3 | Docs state notebooks can use serverless, classic all-purpose compute, or a SQL warehouse for SQL-centric work. |
| 32 | B | 3 | Guidance for automated workloads prefers jobs-oriented compute (serverless or classic jobs) over classic all-purpose. |
| 33 | A | 3 | SQL warehouses are the compute resource designed for SQL analytics/reporting workloads. |
| 34 | B | 3 | When serverless doesn't support required settings/features, classic jobs compute is the standard fallback for jobs. |
| 35 | A | 3 | Databricks describes serverless compute as "versionless" and automatically upgraded by Databricks. |
| 36 | C | 3 | Serverless warehouses support Photon, Predictive IO, and Intelligent Workload Management in the warehouse types comparison. |
| 37 | C | 3 | Classic warehouses support Photon but not Predictive IO or Intelligent Workload Management. |
| 38 | A | 3 | SQL-based ETL executed as SQL tasks aligns with running on a SQL warehouse. |
| 39 | B | 3 | Serverless reduces management and improves startup/scaling; classic provides more configuration control. |
| 40 | A | 3 | Classic SQL warehouse is the baseline option for interactive SQL when serverless/pro aren't options. |
| 41 | A | 3 | For scheduled Spark jobs with Python transformations, serverless jobs compute is the recommended default when supported. |
| 42 | A | 3 | Databricks SQL dashboards and the SQL editor execute on SQL warehouses. |
| 43 | B | 2 | A lakehouse combines lake-like storage scale/open formats with warehouse-like performance and governance. |
| 44 | A | 2 | Delta Lake (tables) and Unity Catalog (governance) are consistently highlighted as foundational in lakehouse-on-Databricks descriptions. |
| 45 | B | 2 | Control plane hosts the workspace app and services; compute plane runs workloads that process data. |
| 46 | B | 2 | Unity Catalog uses a three-level namespace: catalog, schema, object. |
| 47 | A | 2 | Delta Lake is commonly described as Parquet data files plus a transaction log enabling ACID and scalable metadata. |
| 48 | B | 2 | Photon is Databricks' vectorized query engine that accelerates SQL and DataFrame workloads. |
| 49 | B | 2 | Databricks SQL runs on SQL warehouses. |
| 50 | B | 2 | The platform value is unifying workloads on open storage with centralized governance, reducing duplication and data movement. |
