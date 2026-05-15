# Auto Loader (cloudFiles) (Section 2)

## Scope / Objectives (Current exam through May 3, 2026)

Current-exam objectives:
- Classify valid Auto Loader sources and use cases.
- Demonstrate knowledge of Auto Loader syntax.

What this guide covers for the objectives:
- Where Auto Loader fits (incremental ingestion from cloud object storage using Structured Streaming).
- Supported source categories and common "invalid" sources in exam questions.
- Core syntax patterns in PySpark and key options (`cloudFiles.format`, `cloudFiles.schemaLocation`, checkpoints, triggers).
- File discovery modes (directory listing vs file notification) and the newer "file events" direction.

## Terminology Drift Note (Keep current-exam wording visible)

- The current exam (through May 3, 2026) explicitly calls out "Auto Loader". Newer materials sometimes emphasize Lakeflow ingestion features (for example "Lakeflow Connect") and file-events management, but Auto Loader remains a core objective for the current exam.
- Documentation distinguishes:
  - directory listing mode
  - classic file notification mode
  - file notification mode with file events (managed file events)
- Some options are deprecated (for example `cloudFiles.useIncrementalListing`) in favor of newer modes; questions may still reference them.

## Core Concepts

### What Auto Loader is

Auto Loader is an incremental file ingestion capability built on Apache Spark Structured Streaming for cloud object storage. It is designed to efficiently discover and ingest new files as they arrive, with exactly-once processing semantics when used correctly (source + checkpointing + idempotent sink patterns).

Use it when:
- Source data lands as files in cloud object storage (S3, ADLS, GCS, Azure Blob).
- You need incremental discovery (new files) rather than full reprocessing of the entire directory.
- You want schema inference/evolution support with persistent schema tracking.

Not a fit when:
- The source is not file-based cloud object storage (for example Kafka topics).
- You need to handle in-place updates to existing source files as a first-class "update" event.

### Two paths to identify "valid sources" on exams

Auto Loader reads files from cloud object storage locations, often represented by:
- S3 paths like `s3://...` (or `s3a://...` depending on environment)
- ADLS Gen2 paths like `abfss://...`
- GCS paths like `gs://...`
- Azure Blob Storage using event-grid based notifications (when configured)

If a question presents a "source" that is:
- a streaming system (Kafka)
- a database table
- a REST endpoint
- a local file system on your laptop

then it is not a valid Auto Loader *source* in the intended sense for cloud file ingestion (even if data could be staged into files and then ingested).

### Core syntax pattern

In PySpark, Auto Loader uses `cloudFiles` as the source format:
- `.readStream.format("cloudFiles")`
- `.option("cloudFiles.format", "<file_format>")`
- `.option("cloudFiles.schemaLocation", "<path>")` for schema inference and evolution metadata
- `.load("<source_path>")`

Writing the stream:
- `.writeStream.option("checkpointLocation", "<path>")`
- `.toTable("catalog.schema.table")` (common pattern on Databricks)
- Use `trigger(availableNow=True)` to process available data and then stop (common exam-relevant pattern for file ingestion in "batch-like" runs).

### Schema and checkpoint locations are different concepts

Two distinct persistent locations show up in most exam questions:
- `cloudFiles.schemaLocation`: stores schema inference and schema evolution state.
- `checkpointLocation`: stores Structured Streaming checkpoint state for fault tolerance and exactly-once guarantees.

They can be the same path in demos, but in production they are often separated and must be stable across restarts of the same logical stream.

### File discovery modes (high-level)

Directory listing mode:
- Auto Loader lists directories to discover new files.
- Simpler to set up, but can be slower/less scalable for very high file counts.

File notification mode:
- Uses cloud provider event notifications (or managed file events) to discover new files more efficiently.
- Designed to scale to high file arrival rates.

Important restriction to remember:
- In classic file notification mode, Auto Loader does not support changing the source path without risk of missing files.

### Options you should recognize (even if you do not memorize all defaults)

Auto Loader options are specified as `cloudFiles.*` options on the streaming read. Examples that frequently appear in study questions:
- `cloudFiles.includeExistingFiles`: include files already present when the stream starts.
- `cloudFiles.schemaEvolutionMode`: control how schema changes are handled (for example adding columns, rescuing unexpected columns).
- `cloudFiles.inferColumnTypes`: infer types instead of treating everything as strings for some formats.
- `cloudFiles.useNotifications`: enable classic file notification mode (cloud queues/notifications).
- `cloudFiles.useManagedFileEvents`: use managed file events where supported (recommended direction in docs).
- `cloudFiles.cleanSource`: archive or delete ingested files (requires additional permissions and has multi-consumer implications).

## Examples / Decision Tables

### Minimal PySpark example (from source to Delta table)

```python
file_path = "/databricks-datasets/structured-streaming/events"
checkpoint_path = "/tmp/my_autoloader/_checkpoint"

(spark.readStream
  .format("cloudFiles")
  .option("cloudFiles.format", "json")
  .option("cloudFiles.schemaLocation", checkpoint_path)
  .load(file_path)
  .writeStream
  .option("checkpointLocation", checkpoint_path)
  .trigger(availableNow=True)
  .toTable("main.ingest.raw_events"))
```

### Syntax checklist (the lines exams like to omit)

| Intent | You usually need | Why it matters |
|---|---|---|
| Use Auto Loader at all | `.format("cloudFiles")` | This is what makes it Auto Loader (vs file source) |
| Specify file type | `cloudFiles.format` option | Auto Loader must know how to parse files |
| Enable schema inference/evolution tracking | `cloudFiles.schemaLocation` option | Required for inference/evolution state |
| Exactly-once progress tracking | `checkpointLocation` writeStream option | Enables restart/recovery semantics |
| Batch-like ingestion from files | `trigger(availableNow=True)` | Processes current backlog then stops |

### File discovery modes decision table

| Scenario | Better mode | Why |
|---|---|---|
| Low or moderate file volume, simplest setup | Directory listing | Fewer cloud prerequisites |
| Very high file arrival rate or millions of files | File notification / file events | Scales better than listing |
| Need to avoid heavy listing costs | File notification / file events | Avoid repeated directory scans |

### Options quick map (what option belongs where)

| Setting | Where it goes | Purpose |
|---|---|---|
| `cloudFiles.format` | `readStream` option | How to parse files (json, csv, parquet, etc.) |
| `cloudFiles.schemaLocation` | `readStream` option | Persist schema inference/evolution state |
| `cloudFiles.*` options | `readStream` options | Source discovery, parsing, schema handling |
| `checkpointLocation` | `writeStream` option | Persist streaming progress/state for recovery |

### Quick classification table (exam-style)

| Source presented in a question | Valid Auto Loader source? | Notes |
|---|---|---|
| `s3://bucket/path/` | Yes | Cloud object storage directory |
| `abfss://container@acct.dfs.core.windows.net/path/` | Yes | ADLS Gen2 |
| `gs://bucket/path/` | Yes | GCS |
| Kafka topic | No | Not a cloud file directory source |
| Delta table name | No | This is a sink/target, not a file ingestion source |

## Common Exam Traps

- Mixing up the two persistent locations:
  - `cloudFiles.schemaLocation` is not the same as `checkpointLocation`.
- Omitting `cloudFiles.schemaLocation` when relying on schema inference/evolution.
- Assuming Auto Loader is for any "streaming ingestion": it is specifically for incremental file ingestion from cloud object storage.
- Treating "directory listing" as always best: notification-based modes exist specifically for scale.
- Ignoring restrictions: classic file notification mode does not support changing the source path safely.
- Thinking `display()` is a production sink: in notebooks it can trigger a streaming query, but production ingestion should write to a stable sink (Delta table or external system) with checkpointing.
- Missing Unity Catalog access-mode nuance: directory listing is supported broadly, but file notification mode can have stricter compute/access-mode requirements in some configurations.

## Official-Source Links (Databricks)

- Exam guide PDF that includes the current (through May 3, 2026) Section 2 objectives: https://www.databricks.com/sites/default/files/2026-03/databricks-certified-data-engineer-associate-exam-guide-may-4-2026.pdf
- What is Auto Loader?: https://docs.databricks.com/aws/en/ingestion/cloud-object-storage/auto-loader
- Auto Loader options reference: https://docs.databricks.com/aws/en/ingestion/cloud-object-storage/auto-loader/options
- Configure Auto Loader streams in file notification mode: https://docs.databricks.com/aws/en/ingestion/cloud-object-storage/auto-loader/file-notification-mode
- Using Auto Loader with Unity Catalog (access mode considerations): https://docs.databricks.com/aws/en/ingestion/cloud-object-storage/auto-loader/unity-catalog
- Auto Loader tutorial (end-to-end syntax example): https://docs.databricks.com/en/spark/latest/structured-streaming/auto-loader-tutorial.html
- Structured Streaming checkpoints (why checkpointLocation matters): https://docs.databricks.com/gcp/en/structured-streaming/checkpoints.html
- `cloud_files_state` table-valued function (Auto Loader file-level state): https://docs.databricks.com/aws/en/sql/language-manual/functions/cloud_files_state
