# Section 4: Working with Lakeflow Jobs - Practice Exam

Exam guide version: NEW EXAM GUIDE, valid on or after 4-May-2026.

Question style: multiple-choice with four plausible options, modeled after the sample questions in the attached guide. These are original practice questions, not official exam questions.

## Questions

**Question 1**  
**Objective:** Configure Lakeflow Jobs task dependencies  
A data engineer has four tasks: ingest files, clean silver data, aggregate gold metrics, and refresh a dashboard. Which dependency order is most appropriate?

A. `ingest_files -> clean_silver -> aggregate_gold -> refresh_dashboard`  
B. `refresh_dashboard -> aggregate_gold -> clean_silver -> ingest_files`  
C. Run all tasks in parallel with no dependencies.  
D. `clean_silver -> refresh_dashboard -> ingest_files -> aggregate_gold`

**Question 2**  
**Objective:** Configure DAG-based task graph dependencies  
Two independent source ingestion tasks must complete before a shared transformation runs. Which DAG pattern is correct?

A. Make the transformation depend on both ingestion tasks.  
B. Make both ingestion tasks depend on the transformation.  
C. Use a file arrival trigger instead of dependencies.  
D. Put all three tasks in a For each loop without dependencies.

**Question 3**  
**Objective:** Implement retries using Lakeflow Jobs  
A notebook task occasionally fails because of a transient cloud API timeout. Which setting should the data engineer configure?

A. Task retries with a retry interval.  
B. A cross join with the API results.  
C. A dashboard task before the notebook.  
D. A table update trigger only.

**Question 4**  
**Objective:** Implement retries using Lakeflow Jobs  
A task fails every time because of a SQL syntax error. What is the best action?

A. Fix the SQL code because retries do not correct deterministic syntax errors.  
B. Set retries to 100 and ignore the failure.  
C. Replace the task with a dashboard refresh.  
D. Use a file arrival trigger to fix the query.

**Question 5**  
**Objective:** Implement control flow with Run if conditions  
A cleanup task should run after all upstream tasks finish, whether they succeed or fail. Which Run if condition is best?

A. All done  
B. All succeeded  
C. All failed  
D. At least one succeeded

**Question 6**  
**Objective:** Implement control flow with Run if conditions  
An alert task should run only if at least one upstream dependency fails. Which Run if condition should be selected?

A. At least one failed  
B. All succeeded  
C. None failed  
D. At least one succeeded

**Question 7**  
**Objective:** Implement branching with conditional tasks  
A workflow should run transformations only when the ingestion task reports that new rows were loaded. Which task type is most appropriate?

A. If/else condition task  
B. Dashboard task  
C. Cross join task  
D. SQL warehouse task

**Question 8**  
**Objective:** Implement looping with conditional tasks  
A job must run the same notebook once for each region in `["us", "emea", "apac"]`. Which task type should be used?

A. For each task  
B. If/else task with no array input  
C. Dashboard task  
D. File arrival trigger only

**Question 9**  
**Objective:** Configure notebook tasks  
A data engineer wants to run PySpark transformation logic stored in a Databricks notebook. Which Lakeflow Jobs task type should be used?

A. Notebook task  
B. SQL dashboard task  
C. Run Job task only  
D. Unity Catalog grant task

**Question 10**  
**Objective:** Configure SQL query tasks  
A task must run a parameterized SQL aggregation on a SQL warehouse. Which task type is most appropriate?

A. SQL task  
B. Notebook task with no SQL support  
C. For each task only  
D. File arrival trigger

**Question 11**  
**Objective:** Configure dashboard tasks  
A dashboard should refresh after a gold table is rebuilt. How should the dashboard task be placed?

A. Downstream of the gold table build task.  
B. Upstream of ingestion.  
C. In parallel with no dependency on the gold build.  
D. As a replacement for the gold build task.

**Question 12**  
**Objective:** Configure pipeline tasks  
A team has a Lakeflow Spark Declarative Pipeline that builds silver and gold tables. Which task type runs the existing pipeline in a job?

A. Pipeline task  
B. Local file upload task  
C. Dashboard task only  
D. SQL grant task

**Question 13**  
**Objective:** Configure Run Job tasks  
A parent orchestration workflow needs to trigger an existing child job in the same workspace. Which task type should it use?

A. Run Job task  
B. File arrival trigger  
C. SQL view task  
D. Dashboard filter task

**Question 14**  
**Objective:** Implement job schedules  
A source system exports files every day at 1 AM and the pipeline should run at 2 AM. Which trigger is best?

A. Scheduled trigger  
B. Table update trigger  
C. File arrival trigger only  
D. For each task

**Question 15**  
**Objective:** Implement file arrival triggers  
Files arrive unpredictably throughout the day in a Unity Catalog volume. The team wants the job to run when files arrive rather than every five minutes. Which trigger is best?

A. File arrival trigger  
B. Scheduled trigger every five minutes  
C. Manual trigger only  
D. Run if condition

**Question 16**  
**Objective:** Implement table update triggers  
A gold aggregation should run only after the silver `orders` table is updated. Which trigger is most appropriate?

A. Table update trigger  
B. File arrival trigger monitoring a random folder  
C. Scheduled trigger with no regard for source readiness  
D. If/else task as the only trigger

**Question 17**  
**Objective:** Choose time-based vs data-driven triggers  
A dataset is ready at different times each day depending on upstream processing. Which trigger approach usually reduces wasted runs?

A. Data-driven trigger such as table update or file arrival.  
B. Fixed schedule every minute forever.  
C. Manual-only trigger with no monitoring.  
D. Dashboard refresh trigger before source updates.

**Question 18**  
**Objective:** Choose time-based vs data-driven triggers  
A regulatory report must run at 6 AM every business day even if no new files arrive. Which trigger type best matches the requirement?

A. Scheduled trigger  
B. File arrival trigger only  
C. Table update trigger only  
D. For each task

**Question 19**  
**Objective:** Configure file arrival trigger options  
Files arrive in batches over several minutes, and the job should start after the batch settles. Which file arrival option helps?

A. Wait after last change.  
B. All succeeded.  
C. Broadcast join threshold.  
D. Materialized view refresh mode.

**Question 20**  
**Objective:** Configure file arrival trigger options  
A job should not create a new run more than once every 20 minutes even if many files arrive. Which option is relevant?

A. Minimum time between triggers.  
B. At least one failed.  
C. Retry count only.  
D. SQL warehouse size.

**Question 21**  
**Objective:** Configure table update triggers  
A job watches three source tables and should run only after all three have been updated. Which table update trigger setting is needed?

A. Trigger when all monitored tables are updated.  
B. Trigger when any one monitored table is updated.  
C. Trigger only on file arrival.  
D. Disable the trigger.

**Question 22**  
**Objective:** Configure table update triggers  
A job should run when any one of several independent source tables updates. Which setting is appropriate?

A. Trigger when any monitored table is updated.  
B. Trigger only when all monitored tables update.  
C. Run only manually.  
D. Use a dashboard task as the trigger.

**Question 23**  
**Objective:** Configure task dependencies  
A downstream task has two upstream dependencies and uses the default Run if condition. When will it run?

A. After all upstream dependencies succeed.  
B. After the first upstream starts.  
C. Only when all upstream fail.  
D. Before upstream tasks run.

**Question 24**  
**Objective:** Configure task dependencies  
A data engineer wants two enrichment tasks to run at the same time after ingestion completes. How should the DAG be configured?

A. Make both enrichment tasks depend on ingestion and not on each other.  
B. Make one enrichment task depend on the other.  
C. Put the dashboard before ingestion.  
D. Remove ingestion from the workflow.

**Question 25**  
**Objective:** Implement repair and rerun behavior  
A multi-task job fails only at `aggregate_gold`; upstream ingestion and cleaning succeeded. What is the most efficient recovery?

A. Repair the run and rerun the failed task, plus downstream dependent tasks if needed.  
B. Delete the whole job and create a new workspace.  
C. Rerun only the dashboard refresh and skip aggregation.  
D. Ignore the failure because one task succeeded.

**Question 26**  
**Objective:** Implement repair and rerun behavior  
Which CLI command group supports repairing failed job runs?

A. `databricks jobs repair-run`  
B. `databricks catalogs repair-run`  
C. `databricks warehouses deny-run`  
D. `databricks tables trigger-run`

**Question 27**  
**Objective:** Configure common task types  
A Python wheel package contains production ETL code with an entry point. Which task type can run it?

A. Python wheel task  
B. Dashboard task  
C. File arrival trigger  
D. SQL alert only

**Question 28**  
**Objective:** Configure common task types  
A Databricks job must run dbt commands against SQL models. Which task type is relevant?

A. dbt task  
B. For each task only  
C. Local upload task  
D. Unity Catalog schema task

**Question 29**  
**Objective:** Configure common task types  
A Java/Scala application is packaged as a JAR. Which task type is relevant?

A. JAR task  
B. Dashboard task  
C. File arrival trigger only  
D. SQL grant task

**Question 30**  
**Objective:** Configure common task types  
A Spark submit workload needs to run as part of a workflow. Which task type is relevant?

A. Spark Submit task  
B. Table update trigger only  
C. Dashboard task only  
D. Git folder branch

**Question 31**  
**Objective:** Implement control flow with task values  
A notebook task computes a row count and a downstream branch needs to evaluate it. Which mechanism can pass the value?

A. Task values such as `dbutils.jobs.taskValues.set`.  
B. A dashboard color palette.  
C. A SQL warehouse auto-stop setting.  
D. A cluster termination reason.

**Question 32**  
**Objective:** Implement branching with If/else tasks  
A task value `new_rows` is `0`. The workflow should skip expensive gold refresh. Which design is best?

A. If/else condition checks `new_rows > 0`, true branch refreshes gold, false branch does no-op.  
B. Always refresh gold regardless of source changes.  
C. Use a cross join to create rows.  
D. Delete the source table.

**Question 33**  
**Objective:** Implement looping with For each tasks  
Where do For each task run details commonly appear?

A. As iteration-level details for the nested task.  
B. Only in the Delta transaction log.  
C. Only in Catalog Explorer permissions.  
D. Only in a SQL table comment.

**Question 34**  
**Objective:** Configure trigger selection  
A job is controlled by Apache Airflow outside Databricks. Which Lakeflow Jobs trigger setting is reasonable?

A. None/manual, with runs started by API or CLI from Airflow.  
B. File arrival trigger on an unrelated folder.  
C. Table update trigger on a dashboard.  
D. For each task as the trigger.

**Question 35**  
**Objective:** Configure continuous triggers  
A job should keep running by triggering a new run whenever a run completes or fails. Which trigger type describes this behavior?

A. Continuous  
B. Scheduled  
C. File arrival  
D. Table update

**Question 36**  
**Objective:** Monitor DAG-based task graph behavior  
A downstream task is marked `Upstream failed`. What does this usually mean?

A. Its dependency condition was not met because an upstream task failed or did not satisfy the Run if rule.  
B. The downstream task ran successfully.  
C. The task had no dependencies.  
D. The dashboard refreshed too early but succeeded.

**Question 37**  
**Objective:** Configure failure handling  
A notification notebook should run if all upstream tasks are done, even when some fail. Which Run if setting should it use?

A. All done  
B. All succeeded  
C. None failed  
D. At least one succeeded

**Question 38**  
**Objective:** Configure dependencies  
A task must run if either one of two upstream ingestion branches succeeds. Which Run if condition fits?

A. At least one succeeded  
B. All failed  
C. All succeeded  
D. At least one failed

**Question 39**  
**Objective:** Configure dependencies  
A task must run only if none of its upstream dependencies failed. Which Run if condition is relevant?

A. None failed  
B. All failed  
C. At least one failed  
D. All done only

**Question 40**  
**Objective:** Configure retries and timeout  
A task has timeout 20 minutes and max retries 2. What does the timeout apply to?

A. Each retry attempt.  
B. Only the whole job history page.  
C. Only file arrival triggers.  
D. Only dashboard rendering.

**Question 41**  
**Objective:** Choose between trigger types  
A scheduled job often runs when no new files are available. Which change best reduces unnecessary runs?

A. Replace or supplement the schedule with a file arrival trigger.  
B. Increase driver memory.  
C. Use a cross join.  
D. Grant SELECT on the target table.

**Question 42**  
**Objective:** Choose between trigger types  
A job should run after a materialized view updates, not merely when files land. Which trigger should be used?

A. Table update trigger  
B. File arrival trigger  
C. Scheduled trigger only  
D. Notebook widget

**Question 43**  
**Objective:** Configure pipeline tasks  
What should a pipeline task reference?

A. An existing Lakeflow Spark Declarative Pipeline.  
B. A raw local CSV file only.  
C. A Unity Catalog privilege grant only.  
D. A dashboard filter only.

**Question 44**  
**Objective:** Configure dashboard tasks and dependencies  
A dashboard uses `gold.daily_sales`. Which dependency prevents stale dashboard refresh?

A. Make the dashboard task depend on the task that updates `gold.daily_sales`.  
B. Make `gold.daily_sales` depend on the dashboard.  
C. Put dashboard and table task in unrelated jobs with no coordination.  
D. Trigger dashboard before ingestion.

**Question 45**  
**Objective:** Implement loop control  
A pipeline needs to run the same validation notebook for 12 source tables. Which feature avoids manually creating 12 nearly identical tasks?

A. For each task over an array of table names.  
B. All failed Run if condition.  
C. A SQL warehouse restart.  
D. A dashboard subscription.

**Question 46**  
**Objective:** Implement branch control  
A pipeline has a data-quality check task. If it fails, a quarantine handling task should run. Which Run if condition is suitable for the quarantine task?

A. At least one failed  
B. All succeeded  
C. None failed  
D. At least one succeeded

**Question 47**  
**Objective:** Configure Lakeflow Jobs orchestration  
Which statement best describes a Lakeflow Jobs DAG?

A. A graph of task dependencies that defines execution order and possible parallelism.  
B. A Delta table transaction history.  
C. A list of Unity Catalog privileges.  
D. A SQL query result cache.

**Question 48**  
**Objective:** Configure Lakeflow Jobs tasks  
A task runs a notebook that accepts a `process_date` widget. How can a job provide the value?

A. Configure a task parameter for `process_date`.  
B. Rename the cluster to `process_date`.  
C. Put `process_date` in a dashboard title only.  
D. Use `DENY process_date`.

**Question 49**  
**Objective:** Choose time-based vs data-driven triggers  
Which scenario is the best fit for a scheduled trigger?

A. A monthly close report that must run at a fixed calendar time.  
B. Files arriving randomly and rarely.  
C. A downstream table that should update immediately after any source table change.  
D. A cleanup task that runs only after failure.

**Question 50**  
**Objective:** Working with Lakeflow Jobs integrated scenario  
A data engineer needs a workflow that ingests files when they arrive, cleans data, branches only if new rows were loaded, processes each region, and refreshes a dashboard after gold tables update. Which design best fits?

A. File arrival trigger, notebook/SQL tasks in a DAG, If/else branch on new rows, For each over regions, and dashboard task after gold update.  
B. One dashboard task on a fixed schedule before ingestion.  
C. Manual upload, no dependencies, and retries as the only orchestration logic.  
D. Cross join all regions and run dashboard refresh before gold tables exist.

## Answer Key

**Question 1: A**  
A is correct because each task depends on the output of the previous task. B reverses the dependency. C risks stale or missing inputs. D has an invalid order.

**Question 2: A**  
A is correct because the transformation requires both upstream outputs. B reverses execution. C is a trigger, not dependency logic. D loops but does not express the dependency.

**Question 3: A**  
A is correct because retries are useful for transient failures. B, C, and D do not retry the failed notebook task.

**Question 4: A**  
A is correct because syntax errors are deterministic and require code changes. B wastes compute. C and D do not fix SQL syntax.

**Question 5: A**  
A is correct because `All done` runs after dependencies finish regardless of outcome. B requires success. C requires all failures. D requires at least one success.

**Question 6: A**  
A is correct because it runs when one or more upstream tasks fail. B, C, and D handle success-oriented paths.

**Question 7: A**  
A is correct because If/else branches based on a boolean condition. B runs dashboards. C is a data operation. D is compute, not control flow.

**Question 8: A**  
A is correct because For each loops over an input array. B is for branching. C and D are not loop mechanisms.

**Question 9: A**  
A is correct because notebook tasks run Databricks notebooks. B is for dashboards/SQL. C triggers another job. D is permissions, not execution.

**Question 10: A**  
A is correct because SQL tasks run SQL queries on SQL compute. B is too broad and false. C is loop control. D is a trigger.

**Question 11: A**  
A is correct because the dashboard should refresh after data is built. B, C, and D create stale or missing data risk.

**Question 12: A**  
A is correct because pipeline tasks run Lakeflow Spark Declarative Pipelines. B, C, and D are unrelated.

**Question 13: A**  
A is correct because Run Job tasks trigger another existing job. B is a trigger. C and D are unrelated.

**Question 14: A**  
A is correct because the data arrival time is predictable. B and C are data-driven but not needed. D is loop control.

**Question 15: A**  
A is correct because file arrival triggers run when files appear. B can waste runs. C lacks automation. D is not a trigger.

**Question 16: A**  
A is correct because table update triggers respond to table changes. B monitors files, not tables. C may run too early or unnecessarily. D is internal branching, not start logic.

**Question 17: A**  
A is correct because data-driven triggers align runs with data readiness. B wastes runs. C lacks automation. D is ordered incorrectly.

**Question 18: A**  
A is correct because the requirement is time-based. B and C depend on data events. D is not a trigger.

**Question 19: A**  
A is correct because wait-after-last-change delays the run until arrivals settle. B is a dependency condition. C and D are unrelated.

**Question 20: A**  
A is correct because minimum time between triggers limits run frequency. B, C, and D do not control file-trigger frequency.

**Question 21: A**  
A is correct because the job depends on all monitored tables. B can run early. C monitors files. D disables automation.

**Question 22: A**  
A is correct because the sources are independent and any update should start the job. B waits unnecessarily. C and D do not meet the requirement.

**Question 23: A**  
A is correct because the default condition is all upstream dependencies succeeded. B, C, and D are incorrect execution rules.

**Question 24: A**  
A is correct because both branches can run in parallel after ingestion. B serializes them unnecessarily. C and D are incorrect.

**Question 25: A**  
A is correct because repair can rerun failed tasks and needed dependents. B is excessive. C skips required work. D ignores failure.

**Question 26: A**  
A is correct because the Databricks CLI jobs command group includes `repair-run`. B, C, and D are not valid repair commands.

**Question 27: A**  
A is correct because Python wheel tasks run packaged Python entry points. B, C, and D are unrelated.

**Question 28: A**  
A is correct because dbt tasks run dbt workflows. B is only looping. C and D are unrelated.

**Question 29: A**  
A is correct because JAR tasks run packaged Java/Scala code. B, C, and D do not execute JARs.

**Question 30: A**  
A is correct because Spark Submit tasks run spark-submit style workloads. B and C are not task execution types. D is source control.

**Question 31: A**  
A is correct because task values pass computed values to downstream job logic. B, C, and D do not pass task outputs.

**Question 32: A**  
A is correct because the branch avoids unnecessary work when no rows were loaded. B wastes compute. C and D are wrong.

**Question 33: A**  
A is correct because For each exposes nested iteration details. B, C, and D are unrelated metadata locations.

**Question 34: A**  
A is correct because external orchestrators can start jobs by API or CLI. B and C are unrelated triggers. D is loop logic.

**Question 35: A**  
A is correct because continuous trigger mode starts another run when the prior run completes or fails. B, C, and D are different trigger types.

**Question 36: A**  
A is correct because unmet upstream dependency logic prevents the downstream task from running. B, C, and D conflict with the status.

**Question 37: A**  
A is correct because `All done` runs regardless of upstream outcome. B and C require non-failure. D requires a success.

**Question 38: A**  
A is correct because either branch success is enough. B and D are failure-oriented. C requires both succeed.

**Question 39: A**  
A is correct because `None failed` requires no dependency failures. B and C require failures. D is broader and ignores outcome.

**Question 40: A**  
A is correct because task timeout applies to each retry attempt. B, C, and D are unrelated.

**Question 41: A**  
A is correct because file arrival aligns runs to actual files. B, C, and D do not prevent empty scheduled runs.

**Question 42: A**  
A is correct because the dependency is a table update. B monitors files. C may run at the wrong time. D is a parameter mechanism.

**Question 43: A**  
A is correct because pipeline tasks run existing pipelines. B, C, and D are not pipeline task targets.

**Question 44: A**  
A is correct because it prevents dashboard refresh before the gold table update. B, C, and D can produce stale data.

**Question 45: A**  
A is correct because For each avoids duplicating similar tasks. B handles failures. C and D are unrelated.

**Question 46: A**  
A is correct because quarantine should run when the check fails. B, C, and D do not express failure handling.

**Question 47: A**  
A is correct because a DAG defines task execution order and parallelism. B, C, and D are unrelated.

**Question 48: A**  
A is correct because job task parameters can pass widget values. B, C, and D do not provide notebook parameters.

**Question 49: A**  
A is correct because a fixed calendar time is time-based. B and C are data-driven. D is a failure-handling path.

**Question 50: A**  
A is correct because it combines the appropriate trigger, DAG, branch, loop, and dashboard dependency. B runs too early. C lacks dependencies and data-driven orchestration. D is incorrect and unsafe.

