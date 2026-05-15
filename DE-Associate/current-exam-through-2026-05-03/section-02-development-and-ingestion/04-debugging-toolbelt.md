# Debugging Toolbelt (Notebooks, Compute, Logs, Spark UI) (Section 2)

## Scope / Objectives (Current exam through May 3, 2026)

Current-exam objective:
- Use Databricks' built-in debugging tools to troubleshoot a given issue.

What this guide covers for the objective:
- Notebook-level debugging (error tracebacks, interactive debugger, variable explorer).
- Spark UI usage (jobs/stages, streaming tab, batch details).
- Driver/executor logs and compute log delivery.
- Streaming-specific debugging patterns (checkpointing, monitoring, file ingestion state).

## Terminology Drift Note (Keep current-exam wording visible)

- "Clusters" are increasingly referred to as "compute" in docs and UI. Many pages include access-mode permissions and "formerly" labels.
- Notebook debugging is primarily a Python feature (interactive debugger). Scala/SQL/R debugging is usually via outputs, logs, and Spark UI rather than step-through debugging.
- Some debugging topics appear in multiple sections of the blueprint (for example Spark UI optimization also shows up elsewhere). This guide stays focused on tools you use to troubleshoot correctness and runtime failures, not deep performance tuning.

## Core Concepts

### First pass: notebook output is the starting point

Most debugging on Databricks starts in the notebook cell output:
- Python exceptions show a traceback.
- Spark errors often include executor/driver stack traces.
- SQL errors show parser/analysis errors and sometimes query IDs.

Exam habit:
- Identify whether the failure is in the notebook code (syntax/runtime), in Spark execution (distributed job), or in the environment (permissions, missing data, compute config).

### Interactive debugger (Python notebooks)

Databricks notebooks include a built-in interactive debugger for Python:
- Breakpoints, step/continue, variable inspection.
- Debug console to evaluate short expressions when paused.

Notable limitations that show up in questions:
- The debugger is available only for Python.
- The debug console is designed for quick evaluations and can time out.
- The `display` command is not supported in the debug console; use `df.show()` or `df.head()` instead.

### Spark UI is the "distributed truth"

Spark UI is the primary tool to see what Spark is doing:
- Job and stage progress.
- Task failures and error messages.
- Executor details and logs (where permitted).

Streaming:
- If a streaming job is running, the Spark UI shows a Streaming tab.
- Completed batches and batch details help diagnose micro-batch behavior.

### Logs: driver and executor, plus log delivery

Driver logs:
- Useful for exceptions, initialization failures, library conflicts, and top-level errors.

Executor logs:
- Useful when a subset of tasks behave abnormally or fail on specific workers.
- Access can depend on compute access mode and permissions.

Compute log delivery:
- You can configure delivery of driver/worker/event logs to a destination path.
- This helps with post-mortem debugging after compute termination.

Access and security nuance (common exam-style distractor):
- Driver logs can contain sensitive information (for example secrets are not automatically redacted from stdout/stderr).
- Who can view driver logs depends on compute access mode and permissions. Some configurations can broaden or restrict log viewing via `spark.databricks.acl.needAdminPermissionToViewLogs`.

### Streaming troubleshooting basics for file ingestion

Three recurring concepts:
- Checkpoint location: necessary for recovery and exactly-once behavior.
- Source configuration stability: changing key source parameters can require a new checkpoint and can break restarts.
- Use the Spark UI Streaming tab to confirm whether batches are processing and whether input events are arriving.

For Auto Loader file ingestion observability:
- Use `cloud_files_state(...)` to see file-level ingest state for a stream (checkpoint-based).

## Examples / Decision Tables

### Symptom-to-tool quick map

| Symptom in a question | First tool to use | What you are looking for |
|---|---|---|
| Python exception in a cell | Notebook output + debugger | Local stack trace, variable values at breakpoint |
| Job fails with Spark error | Spark UI + driver logs | Failed stage/task, root exception |
| Some tasks fail, others succeed | Spark UI + executor logs | Node-specific failures, OOMs, bad data partitions |
| Streaming query "stuck" or "no data" | Spark UI Streaming tab | Input rate, batch duration, failed batches |
| Issue only after cluster terminates | Log delivery destination | Archived logs and event logs |
| Auto Loader seems to miss or skip files | `cloud_files_state` + checkpoint review | Whether files were discovered/committed, checkpoint continuity |

### Using the notebook debugger effectively (Python)

Typical flow:
1. Enable "Python Notebook Interactive Debugger" in workspace editor settings.
2. Set a breakpoint on the line before the failure.
3. Use "Debug cell" to run and pause at the breakpoint.
4. Inspect variables with the variable explorer.
5. Use the debug console for quick expressions (avoid long-running code; avoid `display`).

### Spark UI: what to look at first

When a job fails:
- Find the failed stage and task.
- Open the task details for the exception message and stack trace.
- Correlate with driver logs for the full error and context.

When a streaming job misbehaves:
- Open Streaming tab.
- Check completed batches and click into a batch for details (input rows, processing time).

### Compute log delivery: exam-relevant facts

Compute log delivery can be configured on compute creation (advanced logging tab) to deliver:
- Spark driver logs
- worker logs
- event logs

It is helpful when:
- You need debugging evidence after compute restarts or termination.
- You are troubleshooting init scripts and library installation issues.

## Common Exam Traps

- Treating the interactive debugger as universal: it is Python-only.
- Using `display()` in the debug console: not supported; use `df.show()` or `df.head()`.
- Debugging a distributed failure only from notebook output: Spark UI and logs usually contain the real root cause.
- Assuming you can always view executor logs: access depends on compute access mode and permissions.
- Forgetting that changing key streaming source parameters (including Auto Loader paths) can require a new checkpoint location.
- Confusing "no rows shown" in notebook output with "no data processed": UI output limits can hide data; Spark UI Streaming tab shows real ingestion activity.

## Official-Source Links (Databricks)

- Exam guide PDF that includes the current (through May 3, 2026) Section 2 objectives: https://www.databricks.com/sites/default/files/2026-03/databricks-certified-data-engineer-associate-exam-guide-may-4-2026.pdf
- Debug notebooks (interactive debugger): https://docs.databricks.com/en/notebooks/debugger.html
- Debugging with the Spark UI (Spark UI + driver/executor logs): https://docs.databricks.com/aws/en/compute/troubleshooting/debugging-spark-ui
- Spark UI optimization guide (how to read Spark UI pages): https://docs.databricks.com/en/optimizations/spark-ui-guide/index.html
- Compute configuration reference (compute log delivery): https://docs.databricks.com/en/compute/configure.html
- Monitoring Structured Streaming queries on Databricks (Streaming tab and progress): https://docs.databricks.com/gcp/en/structured-streaming/stream-monitoring
- Structured Streaming checkpoints (checkpointing and restart behavior): https://docs.databricks.com/gcp/en/structured-streaming/checkpoints.html
- Init script logging (where init script stdout/stderr is written): https://docs.databricks.com/aws/en/init-scripts/logs
- Access control lists (compute abilities and driver log viewing controls): https://docs.databricks.com/aws/en/security/auth/access-control
