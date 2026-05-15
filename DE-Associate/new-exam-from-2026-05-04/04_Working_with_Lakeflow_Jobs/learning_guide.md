# Section 4: Working with Lakeflow Jobs - Learning Guide

Exam guide version: NEW EXAM GUIDE, valid on or after 4-May-2026.

## Section Objectives

This section covers four syllabus bullets from the NEW EXAM GUIDE:

1. Implement control flows, including retries and conditional tasks such as branching and looping, using Lakeflow Jobs for pipeline orchestration.
2. Configure common tasks, including notebook, SQL query, dashboard, and pipeline tasks, and their dependencies using Lakeflow Jobs and its DAG-based task graph.
3. Implement job schedules using Lakeflow Jobs with an understanding of trigger types: scheduled, file arrival, and table update.
4. Choose between time-based and data-driven triggers based on data availability and pipeline dependencies.

## Exam Mental Model

Lakeflow Jobs is Databricks workflow orchestration. A job is a workflow, and a task is one executable step in that workflow. The job's tasks form a directed acyclic graph, or DAG. Upstream tasks run before downstream tasks, and independent branches can run in parallel.

When reading a scenario, identify:

- What work must run: notebook, SQL, dashboard, pipeline, Python script, Python wheel, dbt, JAR, Spark submit, or another job.
- What order it must run in: dependencies and DAG edges.
- What should happen on failure: retry, cleanup, notification, repair, or stop.
- Whether logic branches: if/else, run-if conditions, or loops.
- What starts the workflow: schedule, file arrival, table update, continuous, manual, API, or CLI.

## Core Lakeflow Jobs Concepts

### Job

A job is a Databricks workflow that can contain one or many tasks. Jobs can be run manually, on a schedule, continuously, or when data events occur.

### Task

A task is a unit of work inside a job. Common task types include:

- Notebook task: runs a Databricks notebook.
- SQL task: runs a SQL query, file, alert, or dashboard-related SQL work.
- Dashboard task: refreshes or manages dashboard execution where supported.
- Pipeline task: runs a Lakeflow Spark Declarative Pipeline.
- Python script task: runs a Python file.
- Python wheel task: runs a packaged Python wheel entry point.
- dbt task: runs dbt commands.
- JAR or Spark Submit task: runs JVM or Spark submit workloads.
- Run Job task: triggers another job.
- If/else task: branches based on a boolean expression.
- For each task: loops over an input array and runs a nested task for each element.

Exam focus:

- Notebook tasks are common for PySpark and SQL notebook pipelines.
- SQL tasks are best for SQL query workflows.
- Pipeline tasks run Lakeflow Spark Declarative Pipelines.
- Run Job tasks orchestrate another existing job.
- If/else and For each tasks implement control flow.

## DAG Dependencies

Lakeflow Jobs uses task dependencies to build a DAG. A downstream task can depend on one or more upstream tasks.

Typical pattern:

```text
ingest_orders
  -> clean_orders
  -> aggregate_daily_sales
  -> refresh_dashboard
```

Parallel pattern:

```text
ingest_orders
  -> clean_orders
      -> aggregate_sales
      -> aggregate_customers
          -> refresh_dashboard
```

In the UI, dependencies are shown as lines between task nodes. Tasks without a dependency can run in parallel.

## Run If Conditions

`Run if` conditions determine whether a downstream task runs based on upstream task outcomes.

Common options:

| Run if option | Meaning |
| --- | --- |
| All succeeded | Run only if all upstream dependencies succeeded. This is the default. |
| At least one succeeded | Run if any upstream dependency succeeded. |
| None failed | Run if no upstream dependency failed and at least one upstream ran. |
| All done | Run after all upstream tasks finish, regardless of success or failure. |
| At least one failed | Run only if one or more upstream tasks failed. |
| All failed | Run only if all upstream tasks failed. |

Exam examples:

- Run a cleanup task even after failures: use `All done`.
- Run an alert task when an upstream task fails: use `At least one failed`.
- Run a gold aggregation only when all cleaning tasks succeed: use `All succeeded`.

## Retries and Timeouts

Retries re-run a failed task up to a configured limit. They are useful for transient failures such as temporary service issues, package install problems, or schema evolution restarts in streaming-style jobs.

Task retry settings commonly include:

- Maximum retries.
- Minimum interval between retries.
- Retry timeout behavior.

Timeouts stop a task that runs too long. If both timeout and retries are configured, the timeout applies to each retry attempt.

Exam focus:

- Retries are for transient failures, not bad logic.
- If a task fails because of a permanent SQL syntax error, retries do not fix the root cause.
- Continuous jobs have their own failure/retry behavior with backoff.

## Branching with If/Else Tasks

An If/else task evaluates a condition and sends the workflow down one branch or another.

Use cases:

- Run transformations only when ingestion found new files.
- Run a small-data path or large-data path depending on row count.
- Skip dashboard refresh if no upstream data changed.

Example logic:

```text
ingest_orders -> check_new_rows -> if_new_rows
                                     true  -> clean_orders -> refresh_gold
                                     false -> no_op
```

Tasks can pass values using task values. A notebook can set a task value, and a downstream If/else task can evaluate it.

Python notebook example:

```python
new_rows = spark.table("de_academy.bronze.orders_raw").where("ingestion_date = current_date()").count()
dbutils.jobs.taskValues.set(key="new_rows", value=new_rows)
```

If/else condition concept:

```text
{{tasks.check_new_rows.values.new_rows}} > 0
```

## Looping with For Each Tasks

A For each task runs a nested task once for each element in an input array.

Use cases:

- Process each region in a list.
- Run the same notebook for each source system.
- Refresh multiple independent tables with the same logic.

Example input:

```json
["us", "emea", "apac"]
```

Exam focus:

- For each is for repeated execution over an array.
- It is different from a task dependency.
- In monitoring views, each nested iteration can have its own run details.

## Common Task Types

### Notebook Task

Use a notebook task for notebook-based ETL, data validation, or analysis.

Parameters can be passed into a notebook:

```python
catalog = dbutils.widgets.get("catalog")
source_date = dbutils.widgets.get("source_date")
```

### SQL Task

Use SQL tasks for SQL queries or SQL files, usually running on a SQL warehouse.

Example SQL:

```sql
CREATE OR REPLACE TABLE de_academy.gold.daily_sales AS
SELECT order_date, sum(amount) AS revenue
FROM de_academy.silver.orders
GROUP BY order_date;
```

### Dashboard Task

Use dashboard tasks when a workflow needs to refresh or coordinate dashboard assets after upstream tables are updated.

### Pipeline Task

Use a pipeline task to run a Lakeflow Spark Declarative Pipeline.

Typical pattern:

```text
ingest_files -> run_silver_gold_pipeline -> refresh_dashboard
```

Exam focus:

- A pipeline task runs an existing pipeline.
- Pipeline tasks are different from notebook tasks.
- Use dependencies so downstream dashboards do not refresh before the pipeline completes.

## Schedules and Triggers

Lakeflow Jobs can be triggered in several ways.

| Trigger | Best fit |
| --- | --- |
| Scheduled | Run at a known time or interval. |
| File arrival | Run when new files arrive in a Unity Catalog external location or volume. |
| Table update | Run when one or more source tables are updated. |
| Continuous | Keep a job running by triggering another run when a run completes or fails. |
| Manual/API/CLI | Run on demand or from an external orchestrator. |

### Scheduled Trigger

Use scheduled triggers when data arrives predictably.

Examples:

- Every day at 2 AM after source system export.
- Every hour for hourly aggregates.
- Every Monday for weekly reporting.

### File Arrival Trigger

Use file arrival triggers when files arrive unpredictably and you do not want to poll too often.

Valid monitored locations include Unity Catalog volume paths or external location paths.

Advanced options include:

- Minimum time between triggers: reduces excessive run creation.
- Wait after last change: waits for a batch of files to finish arriving before triggering.

Exam focus:

- File arrival is data-driven.
- It is useful when new files arrive irregularly.
- It monitors governed storage locations.

### Table Update Trigger

Use table update triggers when downstream processing should start after source tables are updated.

Examples:

- Run gold aggregation when silver orders updates.
- Refresh dashboard after a materialized view updates.
- Run a job when all required upstream tables have changed.

Table update triggers can be configured to run when any monitored table updates or when all monitored tables update.

### Time-Based vs Data-Driven Trigger Selection

Choose scheduled triggers when:

- Data availability is predictable.
- A fixed reporting cadence is required.
- Simplicity matters more than immediate reaction.

Choose file arrival or table update triggers when:

- Data arrives unpredictably.
- You want jobs to run only when data is ready.
- Downstream pipelines depend on upstream tables/files.
- You want to reduce wasted runs that find no new data.

## Repair and Rerun

If a multi-task job fails, you do not always need to rerun the entire workflow. You can repair a failed run by rerunning failed tasks and optionally downstream dependent tasks.

CLI example:

```bash
databricks jobs repair-run <run-id> --rerun-all-failed-tasks
```

Run a job manually:

```bash
databricks jobs run-now <job-id>
```

Exam focus:

- Repair keeps the failed run context and reruns selected tasks.
- Rerun failed tasks when upstream successful tasks do not need to repeat.
- Rerun dependent tasks when their outputs depend on rerun tasks.

## Common Exam Traps

- Running a dashboard refresh before the table-building task completes.
- Using scheduled triggers when the scenario says files arrive unpredictably.
- Using file arrival triggers for a source table update dependency.
- Using retries to fix deterministic code errors.
- Forgetting that independent DAG branches can run in parallel.
- Using a cross-job dependency when a simple task dependency is enough.
- Using For each when the requirement is a simple if/else branch.
- Not rerunning dependent tasks after repairing a failed upstream transformation.

## Mini Labs

### Lab 1: Design a Basic DAG

```text
Task 1: ingest_orders notebook
Task 2: clean_orders notebook, depends on ingest_orders
Task 3: create_daily_sales SQL query, depends on clean_orders
Task 4: refresh_sales_dashboard dashboard task, depends on create_daily_sales
```

### Lab 2: Failure Handling

```text
Task: ingest_files
Retries: 2
Retry interval: 5 minutes
Timeout: 30 minutes
```

Use retries for transient errors. If every retry fails with the same syntax error, fix the code.

### Lab 3: Trigger Choice

| Scenario | Trigger |
| --- | --- |
| Daily export completes at 1 AM | Scheduled |
| Files arrive randomly through the day | File arrival |
| Gold table should refresh after silver table updates | Table update |
| External orchestrator controls the run | Manual/API/CLI |

## Official References

- Databricks Certified Data Engineer Associate Exam Guide, May 4, 2026: https://www.databricks.com/sites/default/files/2026-03/databricks-certified-data-engineer-associate-exam-guide-may-4-2026.pdf
- Lakeflow Jobs: https://docs.databricks.com/aws/en/jobs/
- Configure and edit tasks in Lakeflow Jobs: https://docs.databricks.com/aws/en/jobs/configure-task
- Configure task dependencies and Run if conditions: https://docs.databricks.com/aws/en/jobs/run-if
- Control the flow of tasks within Lakeflow Jobs: https://docs.databricks.com/aws/en/jobs/control-flow
- Automating jobs with schedules and triggers: https://docs.databricks.com/aws/en/jobs/triggers
- Trigger jobs when new files arrive: https://docs.databricks.com/aws/en/jobs/file-arrival-triggers
- Trigger jobs when source tables are updated: https://docs.databricks.com/aws/en/jobs/trigger-table-update
- Pipeline task for jobs: https://docs.databricks.com/aws/en/jobs/pipeline
- Run Job task for jobs: https://docs.databricks.com/aws/en/jobs/run-job
- Monitoring and observability for Lakeflow Jobs: https://docs.databricks.com/aws/en/jobs/monitor
- Databricks CLI jobs commands: https://docs.databricks.com/aws/en/dev-tools/cli/reference/jobs-commands

