# Section 6: Troubleshooting, Monitoring, and Optimization - Learning Guide

Exam guide version: NEW EXAM GUIDE, valid on or after 4-May-2026.

## Section Objectives

This section covers five syllabus bullets from the NEW EXAM GUIDE:

1. Identify trends in job performance using the Lakeflow Jobs run history view to compare current execution times against historical baselines.
2. Use the Lakeflow Jobs UI to monitor pipeline health by interpreting job statuses, viewing DAG-based task graphs to spot upstream blockers, and tracking pipeline run times and failure rates.
3. Identify common performance bottlenecks such as data skew, shuffling, and disk spilling by interpreting stage-level metrics in the Spark UI.
4. Understand the features of Liquid Clustering and predictive optimization.
5. Diagnose cluster startup failures, library conflicts, and out-of-memory issues.

## Exam Mental Model

Troubleshooting is evidence-based. Start from the observed symptom, then choose the right diagnostic surface.

| Symptom | First place to look |
| --- | --- |
| Job is slower than usual | Lakeflow Jobs run history and task durations |
| Downstream task did not run | DAG graph, task status, Run if condition |
| One Spark stage is very slow | Spark UI stage page and summary metrics |
| One or a few tasks take much longer | Skew indicators in Spark UI |
| Spill metrics are high | Memory pressure during shuffle |
| Table scans are slow for filtered queries | Liquid clustering, file layout, data skipping, statistics |
| Compute does not start | Compute event log, network, cloud permissions, init scripts |
| Library install/import fails | Library page, installed versions, dependency conflicts, access mode support |
| Driver OOM | Driver logs and code patterns such as large `collect()` |
| Executor OOM | Executor logs, shuffle/spill metrics, partitioning, memory sizing |

## Lakeflow Jobs Run History

The Lakeflow Jobs UI shows run history and task details. Use it to compare current run duration with earlier successful runs.

Important fields:

- Run status: succeeded, failed, canceled, skipped, upstream failed, excluded, disabled, running.
- Start time and end time.
- Duration.
- Trigger type.
- Task-level statuses.
- Task-level run history.
- DAG graph showing dependencies.

Exam focus:

- Compare current execution time against a baseline of successful historical runs.
- Identify which task changed, not just that the whole job became slower.
- A job can fail because an upstream task failed, even if downstream code never started.
- For each tasks expose iteration-level results.

## Monitoring Pipeline Health with the DAG

Use the job DAG to answer:

- Which task failed first?
- Which downstream tasks were blocked?
- Did the failure come from an upstream dependency or the task itself?
- Did a Run if condition skip a task?
- Did a For each iteration fail?

Common statuses:

| Status | Meaning |
| --- | --- |
| Succeeded | Task completed successfully. |
| Failed | Task ran and failed. |
| Upstream failed | Task did not run because dependency requirements were not met. |
| Excluded | Task skipped because its Run if condition was not met. |
| Canceled | Task or run was canceled. |
| Disabled | Task was disabled for the run or in job settings. |

Exam focus:

- Do not debug downstream code first if the downstream task never ran.
- Check upstream task logs and dependency conditions.
- Track failure rate across historical runs to identify recurring instability.

## Spark UI Bottlenecks

The Spark UI is used to inspect Spark jobs, stages, tasks, shuffle, spills, and skew.

### Shuffling

Shuffle happens when Spark redistributes data across partitions. Common causes:

- Joins.
- Group by aggregations.
- Distinct operations.
- Repartitioning.
- Window functions.

Symptoms:

- High shuffle read or shuffle write.
- Long stage duration.
- Network and disk pressure.

Mitigation ideas:

- Broadcast a small dimension table.
- Filter earlier.
- Reduce data volume before wide transformations.
- Use appropriate partitioning.
- Let Databricks choose shuffle partitions with `spark.sql.shuffle.partitions=auto` where supported.

### Data Skew

Skew means one or a few partitions contain much more data than others. In the Spark UI, skew often appears as one or a few tasks taking much longer than the median or 75th percentile.

Common signs:

- Max task duration far larger than median or 75th percentile.
- A few tasks process much more input data.
- Poor cluster utilization near the end of a stage.

Mitigation ideas:

- Use Adaptive Query Execution when supported.
- Salt highly skewed keys.
- Broadcast small lookup tables.
- Pre-aggregate before joins.
- Reconsider join keys or data model.

### Disk Spilling

Spill happens when Spark moves data from memory to disk, often during shuffle or aggregation. Spill is expensive.

Common signs:

- Memory spilled metric.
- Disk spilled metric.
- Long tasks with high shuffle.

Mitigation ideas:

- Reduce data volume before shuffle.
- Increase executor memory only when evidence shows executor memory pressure.
- Increase partitions to reduce per-task data size.
- Optimize joins and aggregations.

Exam focus:

- Skew: uneven task durations.
- Shuffle: high data redistribution.
- Spill: memory pressure leading to disk writes.
- Diagnose using stage-level metrics, not guesses.

## Liquid Clustering

Liquid clustering is a data layout optimization technique for Delta tables. It replaces many static partitioning and Z-ordering patterns.

Benefits:

- Organizes data based on clustering keys.
- Helps filtered queries skip irrelevant data.
- Supports changing clustering keys without rewriting all existing data immediately.
- Works well for growing tables, skewed data, concurrent writes, and changing query patterns.
- Can be enabled with `CLUSTER BY` or `CLUSTER BY AUTO` where supported.

Examples:

```sql
CREATE TABLE de_academy.gold.orders_clustered (
  order_id STRING,
  order_date DATE,
  customer_id STRING,
  amount DECIMAL(10,2)
)
CLUSTER BY (order_date, customer_id);
```

Enable on an existing unpartitioned table:

```sql
ALTER TABLE de_academy.gold.orders
CLUSTER BY (order_date, customer_id);
```

Enable automatic liquid clustering:

```sql
ALTER TABLE de_academy.gold.orders
CLUSTER BY AUTO;
```

Run optimization:

```sql
OPTIMIZE de_academy.gold.orders;
```

Exam focus:

- Liquid clustering is for table data layout and query performance.
- It is recommended for new Delta tables in many Databricks scenarios.
- It is not compatible with partitioning or Z-ordering on the same table.
- Choose clustering keys based on frequent filter columns and query patterns.

## Predictive Optimization

Predictive optimization automatically runs maintenance operations on Unity Catalog managed tables.

Operations:

- `OPTIMIZE`: improves file layout and clustering.
- `VACUUM`: removes unreferenced files after retention rules.
- `ANALYZE`: updates statistics.

Key points:

- Recommended for Unity Catalog managed tables.
- Runs maintenance using serverless jobs compute and has billing implications.
- Does not run on external tables.
- Supports observability through system tables such as predictive optimization operation history.
- Automatic liquid clustering depends on predictive optimization.

Exam focus:

- Predictive optimization reduces manual maintenance burden.
- It applies to Unity Catalog managed tables, not external tables.
- If predictive optimization is enabled, scheduled manual `OPTIMIZE` jobs may be unnecessary.

## Cluster Startup Failures

Common causes:

- Cloud networking or firewall blocks compute from reaching the Databricks control plane.
- Cloud permissions are missing.
- Instance capacity or quota problems.
- Init script failure.
- Library installation failure at startup.
- Metastore or workspace connectivity issue.
- Policy prevents selected configuration.

Evidence to inspect:

- Compute event log.
- Cluster driver logs.
- Init script logs.
- Error class or termination reason.
- Cloud networking and IAM errors.

Exam focus:

- If compute does not start, check the compute event log first.
- Network/firewall issues can cause node daemon timeouts.
- Init script errors can stop startup.
- Compute policies can prevent unsupported settings.

## Library Conflicts

Common causes:

- Incompatible package versions.
- Package already included in Databricks Runtime conflicts with installed version.
- JAR classpath conflicts.
- Unsupported library source for the compute access mode.
- DBFS root library storage deprecated or disabled in newer runtimes.
- Missing access to workspace file or Unity Catalog volume where library is stored.

Best practices:

- Prefer package repositories, workspace files, Unity Catalog volumes, or cloud storage as supported by runtime and access mode.
- Use wheel files for Python packages instead of eggs.
- Use requirements files where supported.
- Restart compute after changing compute-scoped libraries when required.
- Use Databricks Assistant or library error details for package install failures.

Exam focus:

- Library conflicts are not fixed by changing table permissions.
- Check installed library versions and runtime compatibility.
- On Unity Catalog standard access mode, library sources and JAR allowlists matter.

## Out-of-Memory Issues

### Driver OOM

Common causes:

- `collect()` on large DataFrames.
- `toPandas()` on large DataFrames.
- Too many large objects in driver memory.
- Large metadata collection on the driver.

Fixes:

- Process data in Spark instead of collecting it.
- Collect only small aggregated results.
- Write results to Delta tables.
- Increase driver memory only when the driver genuinely needs more memory.

### Executor OOM

Common causes:

- Large shuffle partitions.
- Skewed keys.
- Memory-heavy UDFs.
- Large joins or aggregations.
- Insufficient executor memory for the workload.

Fixes:

- Reduce data before shuffle.
- Increase partitions when per-task data is too large.
- Broadcast small tables.
- Address skew.
- Increase executor memory if evidence supports memory pressure.

Exam focus:

- Driver OOM and executor OOM have different causes.
- Do not solve executor spill by only increasing driver memory.
- Use Spark UI and logs to identify where memory pressure occurs.

## Common Exam Traps

- Looking at downstream task code when the task status says `Upstream failed`.
- Assuming every slow job needs more cluster size.
- Treating skew, shuffle, and spill as the same thing.
- Increasing driver memory for executor-side shuffle spill.
- Using manual `OPTIMIZE` schedules on tables already handled by predictive optimization without checking need.
- Applying liquid clustering to a partitioned table without considering compatibility.
- Treating predictive optimization as available for all table types; external tables are not supported.
- Ignoring library access mode support and runtime compatibility.

## Mini Labs

### Lab 1: Job Trend Review

```text
Open Jobs & Pipelines -> select job -> open run history
Compare current run duration with last five successful runs
Open task details for the slowest changed task
Inspect logs or Spark UI for that task
```

### Lab 2: Spark UI Diagnosis

```text
Open Spark UI -> Stages -> longest stage
Check max duration vs median/75th percentile
Check shuffle read/write
Check memory/disk spill
Decide: skew, shuffle volume, spill, or other I/O issue
```

### Lab 3: Table Layout

```sql
DESCRIBE DETAIL de_academy.gold.orders;
ALTER TABLE de_academy.gold.orders CLUSTER BY AUTO;
OPTIMIZE de_academy.gold.orders;
```

## Official References

- Databricks Certified Data Engineer Associate Exam Guide, May 4, 2026: https://www.databricks.com/sites/default/files/2026-03/databricks-certified-data-engineer-associate-exam-guide-may-4-2026.pdf
- Monitoring and observability for Lakeflow Jobs: https://docs.databricks.com/aws/en/jobs/monitor
- Lakeflow Jobs: https://docs.databricks.com/aws/en/jobs/
- Debugging with the Spark UI: https://docs.databricks.com/aws/en/compute/troubleshooting/debugging-spark-ui
- Spark UI guide for skew and spill: https://docs.databricks.com/aws/optimizations/spark-ui-guide/long-spark-stage-page
- Diagnose high I/O and shuffle in Spark stages: https://docs.databricks.com/aws/en/optimizations/spark-ui-guide/long-spark-stage-io
- Use liquid clustering for tables: https://docs.databricks.com/aws/en/delta/clustering
- Optimize data file layout: https://docs.databricks.com/aws/en/delta/optimize
- Predictive optimization for Unity Catalog managed tables: https://docs.databricks.com/aws/en/optimizations/predictive-optimization
- Troubleshoot compute issues: https://docs.databricks.com/aws/en/compute/troubleshooting
- Install libraries: https://docs.databricks.com/aws/en/libraries/
- Compute-scoped libraries: https://docs.databricks.com/aws/en/libraries/cluster-libraries

