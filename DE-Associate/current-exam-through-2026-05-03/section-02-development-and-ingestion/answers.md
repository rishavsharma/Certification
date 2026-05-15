# Section 2 Answers and Rationales

Section 2 objectives (current exam through May 3, 2026):
- Use Databricks Connect in a data engineering workflow.
- Determine the capabilities of Notebooks functionality.
- Classify valid Auto Loader sources and use cases.
- Demonstrate knowledge of Auto Loader syntax.
- Use Databricks' built-in debugging tools to troubleshoot a given issue.

| Q | Answer | Mapped objective | Rationale |
|---:|:---:|---|---|
| 1 | A | Use Databricks Connect in a data engineering workflow. | Databricks Connect is the Databricks-supported way to run Spark DataFrame code from a local IDE against Databricks compute. |
| 2 | B | Use Databricks Connect in a data engineering workflow. | Your IDE debugger runs locally; Spark DataFrame execution happens remotely on Databricks compute. |
| 3 | C | Use Databricks Connect in a data engineering workflow. | `databricks-connect test` is the official compatibility/config validation command. |
| 4 | C | Use Databricks Connect in a data engineering workflow. | `getOrCreate()` without hard-coded compute parameters keeps code portable between local Connect and in-workspace sessions. |
| 5 | B | Use Databricks Connect in a data engineering workflow. | PAT authentication commonly uses `DATABRICKS_HOST` and `DATABRICKS_TOKEN` (plus compute selection separately). |
| 6 | A | Use Databricks Connect in a data engineering workflow. | Disabling Spark Connect/Databricks Connect on a cluster is done via `spark.databricks.service.server.enabled false`. |
| 7 | B | Use Databricks Connect in a data engineering workflow. | Databricks Connect is built on Spark Connect to enable remote Spark execution via the DataFrame API. |
| 8 | B | Use Databricks Connect in a data engineering workflow. | Hard-coding `serverless()` can create a new serverless session instead of using the attached `spark` on a classic cluster. |
| 9 | C | Determine the capabilities of Notebooks functionality. | Notebook languages run in separate REPL contexts; variables are not automatically shared across languages. |
| 10 | A | Determine the capabilities of Notebooks functionality. | `%md` switches a cell to Markdown. |
| 11 | A | Determine the capabilities of Notebooks functionality. | `%fs` is shorthand for filesystem operations similar to `dbutils.fs`. |
| 12 | B | Determine the capabilities of Notebooks functionality. | `%sh` runs on the driver node only, not on all workers. |
| 13 | B | Determine the capabilities of Notebooks functionality. | `%run` must be in a cell by itself because it executes the entire target notebook inline. |
| 14 | A | Determine the capabilities of Notebooks functionality. | Notebook version history provides snapshots, restore, and side-by-side diffs. |
| 15 | B | Determine the capabilities of Notebooks functionality. | Notebooks support real-time collaboration and comments for shared editing and feedback. |
| 16 | D | Determine the capabilities of Notebooks functionality. | Databricks widgets are `text`, `dropdown`, `combobox`, and `multiselect`; `slider` is not one of the built-in widget types. |
| 17 | A | Determine the capabilities of Notebooks functionality. | The widgets API retrieves values with `dbutils.widgets.get("<name>")`. |
| 18 | B | Determine the capabilities of Notebooks functionality. | Widgets are removed with `dbutils.widgets.remove("<name>")`. |
| 19 | B | Determine the capabilities of Notebooks functionality. | Export formats like HTML, IPYNB, and DBC can include outputs if outputs are not cleared and the export option includes them. |
| 20 | B | Determine the capabilities of Notebooks functionality. | `.dbc` is the Databricks archive format that can include metadata and outputs. |
| 21 | A | Determine the capabilities of Notebooks functionality. | Notebook UI has output and results size limits; the UI may show only a subset even if the query returns more. |
| 22 | B | Determine the capabilities of Notebooks functionality. | Reusable production code is best handled via workspace files/modules or packaged libraries rather than `%run` and copy-paste. |
| 23 | B | Determine the capabilities of Notebooks functionality. | Prefixing the cell with `%sql` is the direct way to make a single cell SQL in a mixed-language notebook. |
| 24 | B | Determine the capabilities of Notebooks functionality. | Creating or editing widgets requires edit permissions on the notebook. |
| 25 | B | Classify valid Auto Loader sources and use cases. | Auto Loader is intended for incremental ingestion of files landing in cloud object storage directories. |
| 26 | A | Classify valid Auto Loader sources and use cases. | Auto Loader is designed to discover and ingest newly arriving files from cloud object storage into targets like Delta tables. |
| 27 | B | Classify valid Auto Loader sources and use cases. | ADLS Gen2 commonly uses `abfss://container@account.dfs.core.windows.net/path/` style URIs. |
| 28 | B | Classify valid Auto Loader sources and use cases. | File notification modes scale by consuming file events rather than relying on repeated directory listing. |
| 29 | B | Use Databricks' built-in debugging tools to troubleshoot a given issue. | The Spark UI Streaming tab appears only when a streaming job is running on that compute. |
| 30 | B | Classify valid Auto Loader sources and use cases. | Classic file notification mode explicitly does not support changing the source path safely and can miss files. |
| 31 | B | Classify valid Auto Loader sources and use cases. | Notification-based modes (and managed file events) are intended to scale for very high file ingestion rates. |
| 32 | A | Classify valid Auto Loader sources and use cases. | Auto Loader is a cloud object storage file ingestion mechanism built on Structured Streaming. |
| 33 | A | Classify valid Auto Loader sources and use cases. | `cloud_files_state(...)` returns file-level state for Auto Loader/read_files streams for observability. |
| 34 | B | Classify valid Auto Loader sources and use cases. | `display()` on a streaming DataFrame starts a streaming job in the notebook context. |
| 35 | B | Demonstrate knowledge of Auto Loader syntax. | Auto Loader in streaming uses `spark.readStream.format("cloudFiles")`. |
| 36 | B | Demonstrate knowledge of Auto Loader syntax. | `cloudFiles.schemaLocation` stores schema inference/evolution state for Auto Loader. |
| 37 | A | Demonstrate knowledge of Auto Loader syntax. | `cloudFiles.format` specifies the incoming file type (json, csv, parquet, etc.). |
| 38 | B | Demonstrate knowledge of Auto Loader syntax. | `checkpointLocation` is the Structured Streaming mechanism for progress/state and recovery. |
| 39 | A | Demonstrate knowledge of Auto Loader syntax. | `availableNow=True` processes all available data and then stops, matching a batch-like ingestion goal. |
| 40 | B | Demonstrate knowledge of Auto Loader syntax. | Checkpointing is what lets a stream restart without reprocessing already committed data. |
| 41 | B | Demonstrate knowledge of Auto Loader syntax. | `checkpointLocation` is configured on `writeStream`, not as a `cloudFiles.*` read option. |
| 42 | B | Demonstrate knowledge of Auto Loader syntax. | Each streaming query must have its own checkpoint directory; sharing checkpoints is unsafe. |
| 43 | B | Demonstrate knowledge of Auto Loader syntax. | Changing input sources (including file paths) is generally not allowed with the same checkpoint and often requires a new checkpoint location. |
| 44 | B | Demonstrate knowledge of Auto Loader syntax. | `schemaLocation` tracks schema inference/evolution; `checkpointLocation` tracks streaming progress/state. |
| 45 | A | Use Databricks' built-in debugging tools to troubleshoot a given issue. | The interactive notebook debugger supports breakpoints and variable inspection for Python notebooks. |
| 46 | B | Use Databricks' built-in debugging tools to troubleshoot a given issue. | The debug console does not support `display`; use `df.show()` or `df.head()` to inspect data. |
| 47 | B | Use Databricks' built-in debugging tools to troubleshoot a given issue. | Spark UI is the place to inspect jobs, stages, tasks, and distributed failure details. |
| 48 | B | Use Databricks' built-in debugging tools to troubleshoot a given issue. | Init script failures are diagnosed via init script stdout/stderr logs (and related cluster logs). |
| 49 | B | Use Databricks' built-in debugging tools to troubleshoot a given issue. | Compute log delivery persists driver/worker/event logs to a configured destination beyond cluster lifetime. |
| 50 | A | Use Databricks' built-in debugging tools to troubleshoot a given issue. | The Spark UI Streaming tab is designed to monitor streaming progress, input rates, and batch details. |
