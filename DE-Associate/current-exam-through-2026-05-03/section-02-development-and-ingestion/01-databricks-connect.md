# Databricks Connect (Section 2)

## Scope / Objectives (Current exam through May 3, 2026)

Current-exam objective:
- Use Databricks Connect in a data engineering workflow.

What this guide covers for the objective:
- What Databricks Connect is and where code runs (local vs remote compute).
- How Databricks Connect is configured and validated (profiles, env vars, CLI auth, tests).
- Common workflow patterns (IDE development, unit-ish testing, promotion into jobs/notebooks).
- Core limitations and common portability traps.

## Terminology Drift Note (Keep current-exam wording visible)

- Documentation increasingly uses "compute" plus "access modes" (`Standard`, `Dedicated`, `No Isolation Shared`) and often includes "formerly" notes. The exam objective still says "Databricks Connect", but the underlying tech is Spark Connect and behaviors differ from classic embedded Spark.
- Databricks recommends OAuth-based authentication in many docs; the exam may still describe token-based (PAT) setups. Know both at a high level.
- Serverless connectivity exists in Databricks Connect (version-gated). Treat it as a "supported compute target" rather than a separate product.

## Core Concepts

### What Databricks Connect is (and is not)

Databricks Connect is a client library that lets you write Spark code in a local environment (IDE, tests, scripts, apps) while executing Spark DataFrame operations on Databricks remote compute.

Key mental model:
- Your Python/Scala/R program runs locally.
- Spark DataFrame operations are planned locally and executed remotely on Databricks compute.
- Data is only brought back to the client when you call actions that materialize results on the client (for example `collect()`, `show()`, `toPandas()`).

Not the same as:
- JDBC/ODBC: those are SQL connectivity tools.
- Databricks REST APIs / SDK: those are control-plane operations (jobs, clusters, workspace, permissions) and do not execute Spark DataFrame code.
- Running a local Spark session: Databricks Connect is remote execution, not local Spark.

### How Databricks Connect is configured

You typically configure three things:
- Workspace identity (host).
- Authentication (PAT or OAuth via unified auth).
- Target compute selection (cluster ID for classic compute, or serverless compute selection).

Common configuration mechanisms (pick one):
- Databricks configuration profile in `~/.databrickscfg` (often `DEFAULT`).
- Environment variables (for example `DATABRICKS_HOST`, `DATABRICKS_TOKEN`, plus `DATABRICKS_CLUSTER_ID` or serverless selection).
- Databricks CLI-authenticated OAuth flow (U2M), referenced by profile.

DatabricksSession:
- Local scripts typically use `DatabricksSession.builder.getOrCreate()` so the same code can run locally and in-workspace.
- In-workspace, `getOrCreate()` returns the already-attached session (the `spark` variable) when no extra connection parameters are passed.

### Configuration examples (profiles and environment variables)

Databricks configuration profile example (`~/.databrickscfg`) using PAT auth:

```ini
[DEFAULT]
host = https://dbc-xxxxxxxx-xxxx.cloud.databricks.com
token = dapiXXXXXXXXXXXXXXXXXXXXXXXX
cluster_id = 0123-456789-abcd123
```

Serverless selection can be done by profile or environment variables. One profile-based pattern is:

```ini
[DEFAULT]
host = https://dbc-xxxxxxxx-xxxx.cloud.databricks.com
token = dapiXXXXXXXXXXXXXXXXXXXXXXXX
serverless_compute_id = auto
```

Environment variable pattern (PAT auth):
- `DATABRICKS_HOST=<workspace-url>`
- `DATABRICKS_TOKEN=<pat>`
- `DATABRICKS_CLUSTER_ID=<cluster-id>` (classic compute) or `DATABRICKS_SERVERLESS_COMPUTE_ID=auto` (serverless)

### Session builder choices (what questions usually probe)

Common patterns:
- `DatabricksSession.builder.getOrCreate()`:
  - Local: uses configured credentials/compute selection.
  - In-workspace: returns the attached `spark` session if no extra connection parameters are passed.
- `DatabricksSession.builder.serverless().getOrCreate()`:
  - Forces a serverless-targeted session and can create a new remote session even when run from a notebook on classic compute.

Exam angle:
- Prefer `getOrCreate()` without hard-coded compute for maximum portability unless the question explicitly requires targeting serverless/another compute.

### Version compatibility is part of the workflow

Databricks Connect versions map to Databricks Runtime versions and have compatibility rules (including for serverless).

Practical exam takeaway:
- If the client package version and the remote compute runtime are incompatible, connection tests fail or workloads behave unexpectedly.

### Validation and troubleshooting basics

Two simple, official checks matter:
- `databricks-connect test` to validate configuration and compatibility.
- `DatabricksSession.builder.validateSession(True).getOrCreate()` (supported in newer Databricks Connect releases) to validate from code.

## Examples / Decision Tables

### Minimal Python example (portable local-to-workspace code)

```python
from databricks.connect import DatabricksSession

# Locally: uses DEFAULT profile/env vars to connect to Databricks compute.
# In a Databricks notebook/job: resolves to the attached 'spark' session.
spark = DatabricksSession.builder.getOrCreate()

df = spark.read.table("samples.nyctaxi.trips")
df.select("pickup_zip", "dropoff_zip").limit(5).show()
```

### Configuration decision table (what to set)

| Goal | Recommended configuration approach | What you set |
|---|---|---|
| Fast local dev against an existing cluster | Profile or env vars | `host`, auth fields, and `cluster_id` (or `DATABRICKS_CLUSTER_ID`) |
| Local dev against serverless compute | Profile + serverless selection | `serverless_compute_id = auto` in profile, or `DATABRICKS_SERVERLESS_COMPUTE_ID=auto`, or `DatabricksSession.builder.serverless()` |
| Keep code deployable to jobs/notebooks | Avoid hard-coding compute | Use `DatabricksSession.builder.getOrCreate()` with `DEFAULT` profile selecting compute |

### Workflow pattern (typical data engineering usage)

1. Develop locally in an IDE using Databricks Connect.
2. Validate connection and version compatibility with `databricks-connect test`.
3. Iterate quickly with local debugging tools while Spark runs remotely.
4. Promote code into:
   - a Databricks notebook (interactive exploration), or
   - a job/task (production runs), ideally without code changes when you used `getOrCreate()`.

## Common Exam Traps

- Confusing where code executes: local Python debugging works, but Spark execution is remote; you cannot "step into" Spark engine internals from the client.
- Assuming all Spark APIs are supported: Spark Connect-based runtimes have API gaps (notably around RDD and SparkContext-style operations).
- Hard-coding compute selection in code (`serverless()` or `clusterId(...)`) and then being surprised when deploying to a different compute target creates a new remote session instead of using the attached `spark`.
- Ignoring authentication drift: PATs still exist, but Databricks documentation often recommends OAuth; understand that profiles and env vars can represent either auth method.
- Skipping validation: `databricks-connect test` is the fastest way to catch mismatch issues (wrong host, expired token, incompatible versions, missing compute settings).
- Using the wrong "Connect": Databricks Connect (Spark execution) is different from Databricks SQL connectors (SQL-only) and from the Databricks SDK (workspace APIs).
- Treating `create()` and `getOrCreate()` as interchangeable: `create()` always makes a new session, and in-workspace it requires explicit connection parameters or it can be unsupported.

## Official-Source Links (Databricks)

- Exam guide PDF that includes the current (through May 3, 2026) Section 2 objectives: https://www.databricks.com/sites/default/files/2026-03/databricks-certified-data-engineer-associate-exam-guide-may-4-2026.pdf
- What is Databricks Connect?: https://docs.databricks.com/en/dev-tools/databricks-connect.html
- Databricks Connect usage requirements (version compatibility, compute requirements): https://docs.databricks.com/aws/en/dev-tools/databricks-connect/requirements
- Compute configuration for Databricks Connect (profiles, env vars, validation, disable service): https://docs.databricks.com/aws/en/dev-tools/databricks-connect/cluster-config
- Tutorial: Run Python code on serverless compute (end-to-end local workflow): https://docs.databricks.com/aws/en/dev-tools/databricks-connect/python/tutorial-serverless
- Databricks Connect support in Databricks notebooks (portability, DatabricksSession behavior): https://docs.databricks.com/aws/en/dev-tools/databricks-connect/python/notebooks
- Spark Connect / access mode limitations (common API gaps such as RDD limitations): https://docs.databricks.com/en/compute/access-mode-limitations.html
