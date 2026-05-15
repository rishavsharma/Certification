# Section 4 (Current Exam Through May 3, 2026): Serverless and Spark UI Troubleshooting

## Scope / Objectives (keep current-exam wording visible)

This guide covers these Section 4 objectives (current exam live **through May 3, 2026**):

- **Use serverless for a hands-off, auto-optimized compute managed by Databricks.**
- **Analyzing the Spark UI to optimize the query.**

## Terminology Drift Note

- Exam says "serverless" in the context of production pipelines. In docs, the relevant term is **serverless compute for workflows** (Lakeflow Jobs).
- Databricks docs provide a step-by-step **Spark UI guide** focused on diagnosing cost and performance issues; treat it as the "official rubric" for Spark UI questions.

## Core Concepts

## Serverless compute for workflows (Lakeflow Jobs)

### What "serverless compute" means on Databricks (exam-level)

Serverless compute is Databricks-managed compute that lets you run notebooks and workflows without provisioning infrastructure in your cloud account. For workflows, Databricks manages compute provisioning, scaling, and (per docs) enables autoscaling and Photon automatically.

Official source: Run your Lakeflow Jobs with serverless compute for workflows.  
https://docs.databricks.com/en/jobs/run-serverless-jobs.html

Official overview: Connect to serverless compute (covers notebooks, workflows, and Lakeflow Spark Declarative Pipelines).  
https://docs.databricks.com/aws/en/compute/serverless

### Requirements and "gotchas"

High-yield requirements for serverless compute for workflows:

- Workspace must have **Unity Catalog enabled**.
- Workloads must support **standard access mode**.

Official source: Run your Lakeflow Jobs with serverless compute for workflows.  
https://docs.databricks.com/en/jobs/run-serverless-jobs.html

### Supported task types (typical exam framing)

Databricks documents that serverless compute is supported for common job task types such as:

- Notebook task
- Python script task
- dbt task
- Python wheel task
- JAR task (documented as preview in the serverless jobs doc)

Official source: Run your Lakeflow Jobs with serverless compute for workflows.  
https://docs.databricks.com/en/jobs/run-serverless-jobs.html

### Limitations (high-yield to memorize)

Serverless compute has limitations that commonly appear as "why did this fail" questions. Examples called out in official docs:

- Streaming trigger limitations (Databricks recommends `Trigger.AvailableNow()` for serverless; other triggers may be unsupported).
- No compute-scoped init scripts, compute-scoped libraries, instance pools, or most Spark configs.
- Environment variables are not supported; Databricks recommends using **widgets** and job/task parameters instead.

Official source: Serverless compute limitations.  
https://docs.databricks.com/aws/en/compute/serverless/limitations

### Dependencies and environment behavior (what the exam might probe)

Operationally relevant serverless details:

- Serverless environments have an environment version and dependency behavior.
- Python virtual environment caching can speed up subsequent tasks in the same run when dependencies match.
- If you change the implementation of a custom Python package used in a serverless job, you must update its **version number** so jobs pick up the latest implementation.

Official source: Configure the serverless environment.  
https://docs.databricks.com/gcp/en/compute/serverless/dependencies

### Compute selection in job tasks (serverless vs classic vs SQL warehouses)

Databricks provides guidance for which compute types are recommended or supported for each task type, and distinguishes between:

- Task-level compute
- Compute shared across tasks

Official source: Configure compute for jobs.  
https://docs.databricks.com/en/jobs/compute.html

## Spark UI analysis (optimization troubleshooting)

### The exam mindset: "find the bottleneck, then classify the cause"

Databricks publishes a step-by-step Spark UI guide. The exam-style approach is usually:

1. Identify the long-running job or stage.
2. Determine whether the stage is dominated by shuffle, spill, skew, or I/O.
3. Use task-level metrics and distribution (p75 vs max) to diagnose skew or spill.

Official index: Diagnose cost and performance issues using the Spark UI.  
https://docs.databricks.com/en/optimizations/spark-ui-guide/index.html

### Longest stage first (what to read on the Stages table)

For the longest stage, Databricks highlights:

- **Input / Output**
- **Shuffle Read / Shuffle Write**

These columns give fast signal about whether the stage is I/O heavy or shuffle heavy.

Official guide: Diagnosing a long job in Spark.  
https://docs.databricks.com/gcp/en/optimizations/spark-ui-guide/long-spark-stage

### Skew vs spill (common testable interpretations)

Databricks suggests checking:

- **Spill**: indicates Spark ran low on memory and moved data to disk (often during shuffle).
- **Skew**: when a small number of tasks are much slower than the rest; look at percentile vs max durations.

Official guide: Skew and spill.  
https://docs.databricks.com/aws/en/optimizations/spark-ui-guide/long-spark-stage-page

## Examples and Decision Tables

### Decision table: choose compute type for a job task

Use this when an exam question asks "what compute should you choose":

| Task type | Recommended compute (per docs) | Notes |
| --- | --- | --- |
| Notebook / Python script / Python wheel | Serverless jobs | Serverless is default for supported tasks; classic compute is still supported |
| SQL | Serverless SQL warehouse | Not "serverless jobs" compute; SQL tasks target a warehouse |
| JAR / Spark submit | Classic jobs (often) | Serverless support is limited; check limitations and supported task types |

Official source: Configure compute for jobs (recommended vs supported compute by task type).  
https://docs.databricks.com/en/jobs/compute.html

### Spark UI diagnosis flow (what to do in the UI)

This is the mental decision tree you should practice:

1. Find the longest stage by duration.
2. Check Stage I/O columns:
   - If Shuffle Read/Write is large, suspect shuffle-heavy operations and then check spill/skew.
3. Open the Stage page:
   - If you see spill, diagnose shuffle spill.
   - If you see skew (max duration much larger than p75), diagnose skew.
4. If neither skew nor spill shows, follow the next steps in the official guide (I/O bound and other causes).

Official guide entry points:

- Spark UI guide index: https://docs.databricks.com/en/optimizations/spark-ui-guide/index.html
- Long stage: https://docs.databricks.com/gcp/en/optimizations/spark-ui-guide/long-spark-stage
- Skew and spill: https://docs.databricks.com/aws/en/optimizations/spark-ui-guide/long-spark-stage-page

## Common Exam Traps

- Assuming serverless compute works for all job types and all Spark settings. Serverless has explicit limitations (init scripts, compute-scoped libraries, many Spark configs, environment variables).
- Forgetting that serverless for workflows has **Unity Catalog** and **standard access mode** requirements.
- Mixing up serverless job compute with SQL warehouses. SQL tasks run on a warehouse, not on "serverless jobs compute".
- Expecting environment variables to work on serverless; Databricks recommends widgets and parameters instead.
- In Spark UI questions, focusing on "job duration" only and ignoring the longest stage and the Stage I/O columns (Input/Output/Shuffle Read/Shuffle Write).
- Misdiagnosing skew: the key signal is uneven task durations (max far larger than percentile), not merely a large shuffle.
- Misdiagnosing spill: spill indicates memory pressure and disk usage, typically due to shuffle or insufficient memory for the working set.

## Official-Source Links (Databricks)

- Run your Lakeflow Jobs with serverless compute for workflows: https://docs.databricks.com/en/jobs/run-serverless-jobs.html
- Connect to serverless compute (overview): https://docs.databricks.com/aws/en/compute/serverless
- Serverless compute limitations: https://docs.databricks.com/aws/en/compute/serverless/limitations
- Configure the serverless environment (dependencies, caching, versioning): https://docs.databricks.com/gcp/en/compute/serverless/dependencies
- Configure compute for jobs (recommended compute by task type): https://docs.databricks.com/en/jobs/compute.html
- Spark UI guide index: https://docs.databricks.com/en/optimizations/spark-ui-guide/index.html
- Diagnosing a long job in Spark: https://docs.databricks.com/gcp/en/optimizations/spark-ui-guide/long-spark-stage
- Skew and spill: https://docs.databricks.com/aws/en/optimizations/spark-ui-guide/long-spark-stage-page

