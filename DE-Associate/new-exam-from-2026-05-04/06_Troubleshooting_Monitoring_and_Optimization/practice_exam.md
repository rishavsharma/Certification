# Section 6: Troubleshooting, Monitoring, and Optimization - Practice Exam

Exam guide version: NEW EXAM GUIDE, valid on or after 4-May-2026.

Question style: multiple-choice with four plausible options, modeled after the sample questions in the attached guide. These are original practice questions, not official exam questions.

## Questions

**Question 1**  
**Objective:** Identify trends in job performance using Lakeflow Jobs run history  
A nightly job usually completes in 18 minutes but took 75 minutes today. Where should the engineer start?

A. Lakeflow Jobs run history and task duration comparison.  
B. Unity Catalog table grants only.  
C. Notebook markdown output only.  
D. Local laptop CPU usage.

**Question 2**  
**Objective:** Identify trends in job performance  
A multi-task job is slower than usual. What should the engineer compare first?

A. Current task durations against historical successful task durations.  
B. Current dashboard color against previous colors.  
C. Current catalog name length against previous length.  
D. Current branch name against the SQL warehouse name.

**Question 3**  
**Objective:** Monitor pipeline health using Lakeflow Jobs UI  
A downstream task did not run and is marked `Upstream failed`. What should the engineer inspect first?

A. The upstream task status, logs, and dependency condition.  
B. The downstream notebook code only.  
C. The Delta table history of an unrelated table.  
D. The Git branch list only.

**Question 4**  
**Objective:** Monitor DAG-based task graphs  
In a job DAG, two tasks can run in parallel when:

A. They have no dependency between them and their upstream requirements are met.  
B. They both write to notebook markdown.  
C. They have a circular dependency.  
D. They are both downstream of a failed task with default conditions.

**Question 5**  
**Objective:** Monitor job statuses  
What does an `Excluded` task usually indicate?

A. The task was skipped because its Run if condition was not met.  
B. The task completed successfully.  
C. The task created a new cluster.  
D. The task updated every source table.

**Question 6**  
**Objective:** Monitor failure rates  
A job fails every Monday but succeeds other days. What is the best monitoring conclusion?

A. There is a recurring trend that should be investigated using run history and task logs.  
B. It is random and should be ignored.  
C. Unity Catalog permissions always fail on Mondays.  
D. The SQL warehouse must be renamed.

**Question 7**  
**Objective:** Interpret Spark UI stage metrics  
A Spark stage shows very high shuffle read and shuffle write. Which operation is a likely cause?

A. A wide transformation such as join or groupBy.  
B. A notebook markdown edit.  
C. A Git pull.  
D. A dashboard title change.

**Question 8**  
**Objective:** Identify data skew in Spark UI  
A stage has 199 tasks finishing in 20 seconds and one task running for 20 minutes. What is the likely issue?

A. Data skew.  
B. Successful broadcast optimization only.  
C. A missing catalog comment.  
D. A pull request conflict.

**Question 9**  
**Objective:** Identify disk spilling in Spark UI  
A stage shows large memory spill and disk spill metrics during aggregation. What does this indicate?

A. Spark ran low on memory and spilled data to disk.  
B. Unity Catalog denied table access.  
C. The job has no shuffle.  
D. The dashboard refreshed successfully.

**Question 10**  
**Objective:** Identify shuffling bottlenecks  
Which transformation commonly causes shuffle?

A. `groupBy("customer_id").agg(sum("amount"))`  
B. `select("order_id")` only  
C. `withColumnRenamed("a", "b")` only  
D. `limit(10)` only

**Question 11**  
**Objective:** Identify data skew remediation  
A join is skewed because one customer ID accounts for most rows. Which mitigation can help?

A. Salt skewed keys or use AQE/skew handling patterns.  
B. Rename the customer ID column only.  
C. Disable all joins.  
D. Grant SELECT to everyone.

**Question 12**  
**Objective:** Identify shuffle remediation  
A large fact table joins to a very small dimension table. Which optimization can reduce shuffle?

A. Broadcast the small dimension table.  
B. Broadcast the large fact table.  
C. Cross join both tables.  
D. Collect the fact table to the driver.

**Question 13**  
**Objective:** Diagnose spill  
Executor spill is high during a shuffle-heavy query. Which action may help after confirming per-task data is too large?

A. Increase shuffle partitions or reduce data before the shuffle.  
B. Increase only notebook font size.  
C. Create a pull request.  
D. Revoke table access.

**Question 14**  
**Objective:** Diagnose driver OOM  
A notebook calls `df.collect()` on a billion-row DataFrame and the driver crashes. What is the best correction?

A. Avoid collecting large data; aggregate or write results using Spark.  
B. Increase table comment length.  
C. Use a file arrival trigger.  
D. Rename the Git branch.

**Question 15**  
**Objective:** Diagnose executor OOM  
Executors fail during a large aggregation with high spill. Which memory setting is most directly relevant if more executor memory is needed?

A. `spark.executor.memory`  
B. `spark.driver.memory` only  
C. `bundle.name`  
D. `USE CATALOG`

**Question 16**  
**Objective:** Diagnose driver OOM  
Which pattern often causes driver OOM?

A. `toPandas()` on a very large DataFrame.  
B. Filtering rows in Spark.  
C. Writing a Delta table using executors.  
D. Creating a Unity Catalog schema.

**Question 17**  
**Objective:** Understand Liquid Clustering  
What is Liquid Clustering used for?

A. Optimizing table data layout for query performance.  
B. Creating Git branches.  
C. Retrying failed jobs.  
D. Masking columns based on user groups.

**Question 18**  
**Objective:** Understand Liquid Clustering  
Which SQL clause enables clustering on a new supported Delta table?

A. `CLUSTER BY (order_date, customer_id)`  
B. `BRANCH BY (order_date)`  
C. `RETRY BY (customer_id)`  
D. `MASK BY (order_date)`

**Question 19**  
**Objective:** Understand Liquid Clustering  
A team wants Databricks to choose clustering keys automatically where supported. Which clause is relevant?

A. `CLUSTER BY AUTO`  
B. `PARTITION BY AUTO AND ZORDER`  
C. `DENY CLUSTERING`  
D. `RUN IF AUTO`

**Question 20**  
**Objective:** Understand Liquid Clustering  
Which statement about Liquid Clustering is correct?

A. It can replace many partitioning and Z-ordering strategies for new Delta tables.  
B. It creates pull requests automatically.  
C. It grants SELECT to analysts.  
D. It fixes all library conflicts.

**Question 21**  
**Objective:** Understand predictive optimization  
Predictive optimization can automatically run which operations on Unity Catalog managed tables?

A. `OPTIMIZE`, `VACUUM`, and `ANALYZE`.  
B. `GRANT`, `DENY`, and `REVOKE` on all users.  
C. `git commit`, `git push`, and `git pull`.  
D. `CREATE BRANCH`, `MERGE PR`, and `DELETE REPO`.

**Question 22**  
**Objective:** Understand predictive optimization  
Which table type is supported by predictive optimization?

A. Unity Catalog managed tables.  
B. All external tables.  
C. Local CSV files on a laptop.  
D. Notebook markdown cells.

**Question 23**  
**Objective:** Understand predictive optimization  
If predictive optimization is already managing table maintenance, what should the team reconsider?

A. Whether scheduled manual `OPTIMIZE` jobs are still needed.  
B. Whether Unity Catalog should be disabled.  
C. Whether all SQL queries should collect to the driver.  
D. Whether Git commits should be skipped.

**Question 24**  
**Objective:** Diagnose cluster startup failures  
A new classic cluster hangs for 25 minutes and the event log says node daemon ping timeout. What category is likely?

A. Compute plane networking or firewall connectivity.  
B. A SQL syntax error in a dashboard.  
C. A view definition problem only.  
D. A `dropDuplicates` issue.

**Question 25**  
**Objective:** Diagnose cluster startup failures  
Where should a data engineer first look when compute fails to start?

A. Compute event log and driver/init script logs.  
B. Dashboard title.  
C. Pull request description.  
D. Gold table row count only.

**Question 26**  
**Objective:** Diagnose cluster startup failures  
An init script exits with an error during cluster startup. What is the likely result?

A. The cluster can fail to start; inspect init script logs.  
B. The job succeeds automatically.  
C. Unity Catalog grants are removed.  
D. Liquid clustering is enabled automatically.

**Question 27**  
**Objective:** Diagnose library conflicts  
A Python package import starts failing after installing a different version of a package already included in Databricks Runtime. What is likely?

A. A library version conflict.  
B. Data skew.  
C. File arrival trigger misconfiguration.  
D. Predictive optimization failure.

**Question 28**  
**Objective:** Diagnose library conflicts  
What is a recommended secure source for libraries in Unity Catalog-enabled workspaces?

A. Unity Catalog volumes or supported package repositories, subject to runtime and access mode support.  
B. DBFS root for all modern runtimes.  
C. Notebook markdown cells.  
D. Dashboard descriptions.

**Question 29**  
**Objective:** Diagnose library conflicts  
A JAR on a standard access mode cluster fails because it is not allowed. What governance feature may be involved?

A. Library or init script allowlist requirements.  
B. Table update triggers.  
C. SQL dashboard filters.  
D. Lakehouse Federation.

**Question 30**  
**Objective:** Diagnose out-of-memory issues  
Which evidence helps distinguish executor OOM from driver OOM?

A. Driver logs, executor logs, Spark UI task metrics, and failure location.  
B. Pull request title only.  
C. Catalog comment length.  
D. Dashboard theme.

**Question 31**  
**Objective:** Monitor Lakeflow Jobs UI  
Which UI view helps identify upstream blockers in a multi-task job?

A. DAG-based task graph.  
B. Git provider profile picture.  
C. Local file explorer only.  
D. SQL query text color.

**Question 32**  
**Objective:** Monitor Lakeflow Jobs UI  
A For each task has one failed iteration. Where should the engineer inspect?

A. Iteration-level run details for the nested task.  
B. Only the bundle README.  
C. Only the table owner field.  
D. Only the cluster name.

**Question 33**  
**Objective:** Identify performance trends  
Which metric is most relevant for comparing current and historical job performance?

A. Task and job duration.  
B. Notebook font size.  
C. Git branch color.  
D. Catalog description length.

**Question 34**  
**Objective:** Identify Spark UI skew  
In Spark UI summary metrics, max task duration is much greater than the 75th percentile. What does this suggest?

A. Possible skew.  
B. Successful local file upload.  
C. A normal perfectly balanced stage.  
D. A Git credentials issue only.

**Question 35**  
**Objective:** Identify Spark UI spill  
What does disk spill usually make a Spark stage do?

A. Run slower because data moves between memory and disk.  
B. Run faster because disk is always faster than memory.  
C. Automatically create a pull request.  
D. Automatically grant Unity Catalog privileges.

**Question 36**  
**Objective:** Diagnose shuffle bottlenecks  
A query spends most time in a join stage with large shuffle read/write. Which first step is best?

A. Inspect join strategy, data volume, skew, and whether one side can be broadcast.  
B. Edit dashboard colors.  
C. Increase driver memory blindly.  
D. Delete Git history.

**Question 37**  
**Objective:** Understand Liquid Clustering  
Which columns are good candidates for clustering keys?

A. Columns frequently used in query filters.  
B. Columns never queried.  
C. Random UUID only for every query pattern.  
D. Notebook names.

**Question 38**  
**Objective:** Understand predictive optimization  
Which system area can provide observability into predictive optimization operations?

A. System tables such as predictive optimization operations history.  
B. Notebook markdown output only.  
C. Git branch list only.  
D. Driver local disk only.

**Question 39**  
**Objective:** Diagnose cluster startup failures  
A cluster startup error references blocked access to cloud STS or object storage services. What should be checked?

A. Cloud IAM and network/firewall path from compute nodes.  
B. Dashboard refresh frequency.  
C. Gold table column names only.  
D. Notebook output height.

**Question 40**  
**Objective:** Diagnose library conflicts  
After uninstalling a compute-scoped library, when might the removal fully take effect?

A. After restarting the compute if required.  
B. Immediately in every existing Python process without restart in all cases.  
C. Only after changing table grants.  
D. Only after creating a pull request.

**Question 41**  
**Objective:** Diagnose OOM issues  
Which fix targets executor-side memory pressure more directly than driver memory?

A. Reduce per-task data, tune partitions, address skew, or increase executor memory based on evidence.  
B. Increase dashboard refresh interval only.  
C. Commit code to Git.  
D. Change catalog owner only.

**Question 42**  
**Objective:** Diagnose OOM issues  
A driver OOM occurs after creating a list of millions of rows in Python. Which approach is better?

A. Keep computation distributed in Spark and avoid large Python driver objects.  
B. Use a larger SQL comment.  
C. Apply column masking.  
D. Enable file arrival triggers.

**Question 43**  
**Objective:** Monitor Lakeflow Jobs health  
A job has increasing failure rate over the last week. What should the engineer do?

A. Compare failed runs, identify common failing task/status, and inspect logs.  
B. Ignore because one run succeeded last month.  
C. Rename all notebooks.  
D. Convert all tables to CSV.

**Question 44**  
**Objective:** Understand optimization features  
Which command physically optimizes Delta file layout?

A. `OPTIMIZE table_name`  
B. `GRANT SELECT ON TABLE table_name`  
C. `CREATE BRANCH table_name`  
D. `RUN IF table_name`

**Question 45**  
**Objective:** Understand optimization features  
What operation removes unreferenced data files after retention rules?

A. `VACUUM`  
B. `BROADCAST`  
C. `COMMIT`  
D. `PULL`

**Question 46**  
**Objective:** Understand optimization features  
What operation updates table statistics for query optimization?

A. `ANALYZE`  
B. `DENY`  
C. `PUSH`  
D. `RUN NOW`

**Question 47**  
**Objective:** Diagnose performance regression  
A job got slower after input data volume doubled. What is the best next step?

A. Compare task metrics and Spark UI stages to see which stage and operation regressed.  
B. Assume permissions are wrong.  
C. Delete the workspace.  
D. Change only notebook titles.

**Question 48**  
**Objective:** Monitor task graph blockers  
A downstream task is skipped because its Run if condition was not met. Which status may appear?

A. Excluded  
B. Succeeded  
C. Running forever by definition  
D. Optimized

**Question 49**  
**Objective:** Diagnose library conflicts  
A package installation fails on a compute library page. Which built-in aid may help?

A. Databricks Assistant diagnose error flow where available.  
B. SQL `COUNT(*)`.  
C. Liquid clustering only.  
D. File arrival trigger.

**Question 50**  
**Objective:** Troubleshooting integrated scenario  
A job is slower, the Spark UI shows high shuffle, one task is much slower than others, and disk spill is high. Which diagnosis is most defensible?

A. The job likely has a shuffle-heavy stage with skew and memory pressure; inspect join/aggregation strategy, skew handling, partitions, and spill.  
B. The only possible cause is missing dashboard permissions.  
C. Git branches are causing Spark tasks to skew.  
D. Predictive optimization deleted the job.

## Answer Key

**Question 1: A**  
A is correct because run history shows duration trends and task breakdowns. B, C, and D do not diagnose job runtime trends.

**Question 2: A**  
A is correct because task-level comparison identifies the changed step. B, C, and D are irrelevant.

**Question 3: A**  
A is correct because `Upstream failed` means dependency logic blocked the downstream task. B starts too late in the chain. C and D are unrelated.

**Question 4: A**  
A is correct because independent tasks can run in parallel after upstream requirements are met. B, C, and D are false.

**Question 5: A**  
A is correct because `Excluded` commonly means skipped by Run if logic. B, C, and D are not the meaning.

**Question 6: A**  
A is correct because a recurring weekday failure is a trend. B ignores evidence. C and D are unsupported assumptions.

**Question 7: A**  
A is correct because joins and aggregations commonly shuffle data. B, C, and D do not.

**Question 8: A**  
A is correct because one very slow task among many fast tasks is a skew symptom. B, C, and D are unrelated.

**Question 9: A**  
A is correct because spill means memory pressure caused disk writes. B, C, and D are false.

**Question 10: A**  
A is correct because groupBy aggregations are wide transformations. B, C, and D are narrow/simple operations.

**Question 11: A**  
A is correct because salting/AQE can address skewed keys. B, C, and D do not fix skew.

**Question 12: A**  
A is correct because broadcasting the small side avoids large shuffle. B is dangerous. C and D are inefficient or unsafe.

**Question 13: A**  
A is correct because reducing per-task data or increasing partitions can reduce spill. B, C, and D are unrelated.

**Question 14: A**  
A is correct because collecting huge data to the driver causes driver OOM. B, C, and D do not solve it.

**Question 15: A**  
A is correct because executor memory affects executor-side work. B is driver-side. C and D are unrelated.

**Question 16: A**  
A is correct because `toPandas()` collects data to the driver. B, C, and D are distributed or metadata operations.

**Question 17: A**  
A is correct because Liquid Clustering optimizes table layout. B, C, and D are different features.

**Question 18: A**  
A is correct because `CLUSTER BY` sets clustering keys. B, C, and D are invalid.

**Question 19: A**  
A is correct because `CLUSTER BY AUTO` enables automatic key selection where supported. B, C, and D are invalid.

**Question 20: A**  
A is correct because Liquid Clustering can replace many partition/Z-order patterns. B, C, and D are false.

**Question 21: A**  
A is correct because predictive optimization runs maintenance operations. B, C, and D are unrelated.

**Question 22: A**  
A is correct because predictive optimization supports UC managed tables. B is false for external tables. C and D are not tables.

**Question 23: A**  
A is correct because managed maintenance may make manual schedules redundant. B, C, and D are wrong.

**Question 24: A**  
A is correct because node daemon timeout points to compute connectivity. B, C, and D are unrelated.

**Question 25: A**  
A is correct because startup failures are visible in event and startup logs. B, C, and D are not startup diagnostics.

**Question 26: A**  
A is correct because failing init scripts can block startup. B, C, and D are false.

**Question 27: A**  
A is correct because changing package versions can cause dependency conflicts. B, C, and D are unrelated.

**Question 28: A**  
A is correct because UC volumes and package repos are supported secure sources depending on runtime/access mode. B is deprecated/disabled in newer runtimes. C and D are invalid.

**Question 29: A**  
A is correct because standard access mode can require allowlisted JARs/Maven coordinates. B, C, and D are unrelated.

**Question 30: A**  
A is correct because logs and Spark UI locate the failing process. B, C, and D do not.

**Question 31: A**  
A is correct because DAG graph shows blockers. B, C, and D are unrelated.

**Question 32: A**  
A is correct because For each exposes nested iteration details. B, C, and D do not.

**Question 33: A**  
A is correct because duration is the central performance trend metric. B, C, and D are irrelevant.

**Question 34: A**  
A is correct because max duration far above percentile suggests skew. B, C, and D are wrong.

**Question 35: A**  
A is correct because disk spill is slower than in-memory processing. B, C, and D are false.

**Question 36: A**  
A is correct because high shuffle in a join requires examining join strategy, volume, and skew. B, C, and D are poor first steps.

**Question 37: A**  
A is correct because clustering keys should match filter patterns. B, C, and D are poor choices.

**Question 38: A**  
A is correct because predictive optimization has system table observability. B, C, and D are not relevant.

**Question 39: A**  
A is correct because STS/object storage errors point to cloud IAM/network paths. B, C, and D are unrelated.

**Question 40: A**  
A is correct because compute-scoped library changes can require restart. B is too broad. C and D are unrelated.

**Question 41: A**  
A is correct because executor pressure needs distributed execution fixes. B, C, and D are unrelated.

**Question 42: A**  
A is correct because driver memory pressure comes from large local Python objects. B, C, and D do not address the cause.

**Question 43: A**  
A is correct because failure trends require comparing runs and logs. B, C, and D ignore the problem.

**Question 44: A**  
A is correct because `OPTIMIZE` rewrites files to improve layout. B, C, and D are unrelated.

**Question 45: A**  
A is correct because `VACUUM` removes unreferenced files after retention. B, C, and D are unrelated.

**Question 46: A**  
A is correct because `ANALYZE` updates statistics. B, C, and D do not.

**Question 47: A**  
A is correct because volume changes require metrics-based diagnosis. B, C, and D are unsupported.

**Question 48: A**  
A is correct because `Excluded` can mean skipped by condition. B, C, and D are wrong.

**Question 49: A**  
A is correct because Databricks Assistant can diagnose some library install errors. B, C, and D do not.

**Question 50: A**  
A is correct because the evidence points to shuffle, skew, and spill. B, C, and D are unsupported.

