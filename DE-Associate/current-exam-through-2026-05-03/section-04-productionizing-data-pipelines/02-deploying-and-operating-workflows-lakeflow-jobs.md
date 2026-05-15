# Section 4 (Current Exam Through May 3, 2026): Deploying and Operating Workflows (Lakeflow Jobs)

## Scope / Objectives (keep current-exam wording visible)

This guide targets the Section 4 objective (current exam live **through May 3, 2026**):

- **Deploy a workflow, repair, and rerun a task in case of failure.**

This guide intentionally focuses on **operational behavior** (deploy, run, repair, rerun) and the "how it actually behaves" details that show up on exam questions.

## Terminology Drift Note

- Exam says **workflow**. In docs/UI you will see **Lakeflow Jobs** and the **Jobs & Pipelines** UI.
- "Rerun" and "repair run" are sometimes used casually as synonyms, but Databricks has a specific **repair run** feature with distinct behavior.

## Core Concepts

### Job vs run vs task (the minimum you must distinguish)

- A **job** is the configured workflow definition (tasks, dependencies, compute, schedule).
- A **run** is one execution instance of that job.
- A **task** is a node in a multi-task job (notebook task, Python script task, wheel task, SQL task, pipeline refresh task, and so on).

Official job configuration overview: https://docs.databricks.com/aws/en/jobs/configure-job

### Deployment of "workflows" (what counts as deployment on the exam)

The exam's "deploy a workflow" phrasing commonly maps to one of these official approaches:

- **UI-based job creation** in Jobs & Pipelines.
- **Developer tools**: Databricks CLI, REST API, SDKs.
- **Bundles (DAB / Asset Bundles)**: deploy jobs and related artifacts as a project.

Official source: Configure and edit Lakeflow Jobs (explicitly lists UI, CLI, REST API, and bundles).  
https://docs.databricks.com/aws/en/jobs/configure-job

Official source: Develop a job with bundles tutorial (validate, deploy, run).  
https://docs.databricks.com/en/jobs/how-to/use-bundles-with-jobs.html

### "Repair run" behavior (high-yield)

Repair runs are designed to fix a failed multi-task run without re-running everything.

Key properties you should memorize:

- A repair run re-runs selected tasks **as part of the original run**.
- Repair uses the **current job and task settings** (not necessarily the exact historical settings at the time of the original run).
- The run you repair must **not** be in progress.
- Repair is supported only for jobs orchestrating **two or more tasks**.

Official CLI behavior summary: `databricks jobs repair-run` (current settings; part of original run; cannot be in progress).  
https://docs.databricks.com/gcp/en/dev-tools/cli/reference/jobs-commands

Official UI troubleshooting: Repair is supported only with multi-task jobs; repair run may create new job clusters in certain cases.  
https://docs.databricks.com/gcp/en/jobs/repair-job-failures

### Repair run and compute (job cluster versioning)

Important operational nuance:

- If one or more tasks share a job cluster, a **repair run creates a new job cluster** (for example `my_job_cluster_v1`) whose settings match the *current* settings for the original job cluster spec.

Official source: Troubleshoot and repair job failures.  
https://docs.databricks.com/gcp/en/jobs/repair-job-failures

### Rerun options (typical exam framing)

In exam scenarios, you'll usually see one of these intent patterns:

- "Re-run only what failed and what depends on it" (repair run with dependent tasks).
- "Re-run everything that failed in the job" (repair run all failed tasks).
- "Start a fresh run with new parameters" (new run, not a repair).

The CLI includes options that correspond to these patterns (for example, rerun all failed tasks; rerun dependent tasks).  
Official CLI reference: https://docs.databricks.com/gcp/en/dev-tools/cli/reference/jobs-commands

### Operational views: where you confirm what happened

Databricks emphasizes operational UI patterns that show up in troubleshooting questions:

- The **Runs** / **Job Runs** UI and its matrix view for multi-task jobs (quickly identify failures and skipped downstream tasks).
- The **Timeline** view for runs (task-level runtimes and progress).

Official source for repair/troubleshoot flow and run matrix concepts: https://docs.databricks.com/gcp/en/jobs/repair-job-failures

## Examples and Decision Tables

### Decision table: "New run" vs "Repair run"

| Situation | Best action | Why |
| --- | --- | --- |
| A single task failed due to transient issue; downstream tasks were skipped | Repair run of failed task(s) and dependent tasks | Keeps successful work from the original run and avoids re-running everything |
| You changed the job definition (task code, parameters, compute) and want to run end-to-end with the new settings | New run | Repair reuses the original run context and is intended for recovery, not "new pipeline execution" |
| You need at-most-once semantics and the workflow is not idempotent | Prefer controlled new run strategy (and disable auto retries where applicable) | Repair and retry behaviors can violate "exactly once" assumptions if the pipeline is not designed for re-execution |

Repair run properties are explicitly documented (multi-task requirement; new job cluster on repair in some cases).  
https://docs.databricks.com/gcp/en/jobs/repair-job-failures

### Example: Operational sequence (bundle-based job deployment)

From the official tutorial, the "standard" bundle flow is:

1. `databricks bundle validate`
2. `databricks bundle deploy --target <target>`
3. `databricks bundle run --target <target> <resource_key>`

Official tutorial (jobs + bundles): https://docs.databricks.com/en/jobs/how-to/use-bundles-with-jobs.html

### Example: Repair a run with the Databricks CLI

The CLI provides `databricks jobs repair-run RUN_ID` which repairs a run that is not in progress and re-runs tasks as part of the original run.

Official CLI reference: https://docs.databricks.com/gcp/en/dev-tools/cli/reference/jobs-commands

### Quick checklist: before you choose "repair"

- Is it a multi-task job (2+ tasks)? Repair is supported only then.
- Is the run terminated (not in progress)?
- Do you understand that the repair will use **current** job settings?
- Do you need dependent tasks re-run as well (because upstream output changed)?

## Common Exam Traps

- Mixing up "rerun a job" with "repair run." Repair runs are special and occur **within the original run history**.
- Forgetting repair is only supported for **multi-task** jobs.
- Expecting a repair run to use "the exact old cluster." If tasks share a job cluster, the repair run can create a **new versioned job cluster** with current settings.
- Attempting to repair a run that is still **in progress**.
- Assuming repair reruns everything by default. Repair can be scoped; there are options to rerun failed tasks and optionally dependent tasks.
- Confusing deployment with execution: bundles and other IaC-like tools generally require a separate "run" step after "deploy".

## Official-Source Links (Databricks)

- Configure and edit Lakeflow Jobs (UI + repair/rerun mention): https://docs.databricks.com/aws/en/jobs/configure-job
- Troubleshoot and repair job failures (repair run mechanics; multi-task requirement; job cluster versioning): https://docs.databricks.com/gcp/en/jobs/repair-job-failures
- Jobs CLI command reference (`jobs repair-run`, options, and semantics): https://docs.databricks.com/gcp/en/dev-tools/cli/reference/jobs-commands
- Jobs REST API 2.0 reference (for programmatic run management): https://docs.databricks.com/en/dev-tools/api/2.0/jobs.html
- Develop a job with bundles (validate/deploy/run/destroy): https://docs.databricks.com/en/jobs/how-to/use-bundles-with-jobs.html

