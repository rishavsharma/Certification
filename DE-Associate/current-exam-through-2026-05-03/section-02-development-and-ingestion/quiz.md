# Section 2 Quiz (Development and Ingestion)

## Multiple Choice Questions (50)

1. A data engineer wants to develop PySpark transformations locally in VS Code while executing Spark DataFrame operations on Databricks compute. Which tool best fits this workflow?
   A. Databricks Connect
   B. JDBC/ODBC driver
   C. Databricks REST API only
   D. Local Spark standalone cluster

2. In a Databricks Connect workflow, where does the Python debugger (breakpoints, step-through) primarily run?
   A. On the remote Databricks driver JVM
   B. Locally in the IDE, while Spark plans run remotely
   C. On the Databricks control plane
   D. Only inside Databricks notebooks

3. Which command is designed to quickly validate that Databricks Connect is correctly configured and compatible with the target compute?
   A. `spark.sql("SELECT 1")`
   B. `databricks workspace ls`
   C. `databricks-connect test`
   D. `%pip install databricks-connect`

4. A data engineer wants the same code to run both locally (via Databricks Connect) and inside a Databricks notebook without modifications. Which Spark session construction pattern best supports this portability?
   A. Always use `DatabricksSession.builder.serverless().getOrCreate()`
   B. Always use `SparkSession.builder.master("local[*]").getOrCreate()`
   C. Use `DatabricksSession.builder.getOrCreate()` and avoid hard-coding compute selection
   D. Use JDBC to run all code as SQL

5. Which environment variables are commonly used for Databricks personal access token (PAT) authentication with Databricks Connect?
   A. `DATABRICKS_USER` and `DATABRICKS_PASSWORD`
   B. `DATABRICKS_HOST` and `DATABRICKS_TOKEN`
   C. `SPARK_HOME` and `HADOOP_CONF_DIR`
   D. `DATABRICKS_CLUSTER_NAME` and `DATABRICKS_ORG_ID`

6. A team disables Databricks Connect services on a classic cluster to enforce notebook-only usage. Which Spark configuration setting is used to disable the service?
   A. `spark.databricks.service.server.enabled false`
   B. `spark.sql.warehouse.dir false`
   C. `spark.databricks.delta.preview.enabled false`
   D. `spark.databricks.connect.disabled true`

7. Which statement best describes the relationship between Databricks Connect and Spark Connect?
   A. Databricks Connect is a JDBC wrapper around Spark SQL.
   B. Databricks Connect is built on Spark Connect for remote Spark DataFrame execution.
   C. Spark Connect is deprecated and replaced by Databricks Connect.
   D. They are unrelated products.

8. A data engineer hard-codes `DatabricksSession.builder.serverless()` in a library and then runs that library from a notebook attached to a classic cluster. What is the most likely outcome?
   A. The notebook always uses the attached `spark` session with no new remote session.
   B. A new serverless Spark session can be created instead of using the attached session.
   C. The code automatically falls back to local Spark execution.
   D. The code runs as SQL on a warehouse.

9. A notebook contains both Python and SQL cells. A variable `x` is defined in a `%python` cell. What is the default behavior when a `%sql` cell tries to reference `x`?
   A. `%sql` can reference `x` automatically because languages share one REPL.
   B. `%sql` can reference `x` only if `x` is broadcasted.
   C. `%sql` cannot reference `x` because languages have separate execution contexts.
   D. `%sql` can reference `x` only if `x` is a string.

10. Which magic command is used to change a cell to Markdown in a Databricks notebook?
   A. `%md`
   B. `%mk`
   C. `%markdown`
   D. `%doc`

11. A data engineer needs to list files in a workspace-accessible filesystem location from a notebook quickly. Which magic is a shorthand for filesystem commands similar to `dbutils.fs`?
   A. `%fs`
   B. `%ls`
   C. `%dir`
   D. `%files`

12. Which statement about `%sh` in Databricks notebooks is correct?
   A. It runs shell commands on all worker nodes.
   B. It runs shell commands only on the driver node.
   C. It runs shell commands only on executors.
   D. It runs shell commands on the Databricks control plane.

13. A notebook uses `%run /Shared/utilities` to import helper functions. Which rule must be followed for `%run` to work as intended?
   A. `%run` must be the last line in a multi-command cell.
   B. `%run` must be in its own cell because it executes the entire notebook inline.
   C. `%run` can only import Python files, not notebooks.
   D. `%run` only works from SQL cells.

14. Which feature provides automatic notebook snapshot history with the ability to restore and view diffs?
   A. Notebook version history
   B. Delta time travel
   C. Cluster log delivery
   D. Unity Catalog lineage

15. Two engineers need to edit the same notebook at the same time and leave threaded feedback on specific lines. Which notebook capability supports this?
   A. Compute autostart
   B. Real-time collaboration and comments
   C. Auto Loader schema evolution
   D. Databricks Connect validation

16. Which widget type is NOT one of the four Databricks widgets types?
   A. `text`
   B. `dropdown`
   C. `multiselect`
   D. `slider`

17. A notebook has a widget named `state`. Which call retrieves its current value in Python?
   A. `dbutils.widgets.get("state")`
   B. `dbutils.widgets.read("state")`
   C. `dbutils.widgets.value("state")`
   D. `widgets.get("state")`

18. A data engineer wants to remove a widget named `state` from a notebook. Which API call is correct?
   A. `dbutils.widgets.delete("state")`
   B. `dbutils.widgets.remove("state")`
   C. `dbutils.widgets.drop("state")`
   D. `dbutils.widgets.clear("state")`

19. Which statement about exporting a notebook is correct?
   A. Export always excludes cell outputs.
   B. Export to HTML, IPYNB, or DBC can include outputs if outputs are not cleared and the option is selected.
   C. Export supports only `.dbc` format.
   D. Export supports only source `.py` format.

20. Which format is a Databricks binary archive that can include notebook metadata and command outputs?
   A. `.ipynb`
   B. `.dbc`
   C. `.html`
   D. `.py`

21. A data engineer expects a notebook result table to show 200,000 rows but sees only a subset. Which notebook limitation most directly explains this?
   A. Table results are limited in the notebook UI to a maximum size/row count.
   B. Spark cannot return more than 10,000 rows in any query.
   C. Delta tables cannot contain more than 10,000 rows.
   D. Widgets restrict output sizes.

22. A team wants to reuse production logic across notebooks and jobs without relying on `%run`. Which approach is most aligned with Databricks guidance?
   A. Copy-paste code into every notebook
   B. Use workspace source files/modules or package code as a library and install it
   C. Store code only in notebook comments
   D. Put all logic into `%sh` scripts

23. A notebook's default language is Python, but one cell should be SQL. Which is the most direct way to make only that cell SQL?
   A. Convert the whole notebook to SQL
   B. Prefix the cell with `%sql`
   C. Add `spark.sql` around the cell content
   D. Use `%md` and embed SQL in Markdown

24. Which permission is most relevant for a user to add or edit widgets in a notebook?
   A. CAN VIEW permissions on the cluster
   B. CAN EDIT permissions on the notebook
   C. CAN MANAGE permission on the metastore
   D. Ownership of the workspace

25. A pipeline needs to incrementally ingest new JSON files that arrive in a cloud object storage directory. Which source is the intended target for Auto Loader?
   A. Kafka topic
   B. Cloud object storage directory path
   C. Delta table name
   D. JDBC connection URL

26. Which of the following is a valid use case for Auto Loader?
   A. Incrementally ingest new files arriving in cloud object storage into a Delta table
   B. Perform row-level updates on a source database table as CDC without staging to files
   C. Execute BI dashboards against a SQL warehouse
   D. Create and manage job schedules

27. Which cloud storage path style best matches an Azure Data Lake Storage Gen2 (ADLS) source commonly used with Auto Loader?
   A. `gs://bucket/path/`
   B. `abfss://container@account.dfs.core.windows.net/path/`
   C. `jdbc:databricks://host:443/default`
   D. `kafka://broker:9092/topic`

28. In file notification mode, what is the primary advantage over directory listing mode?
   A. It avoids checkpoints entirely.
   B. It can scale better by subscribing to file events rather than repeatedly listing directories.
   C. It allows Auto Loader to read from Kafka.
   D. It guarantees schema never changes.

29. A streaming Auto Loader job is running, but the Spark UI does not show the Streaming tab for that compute. What is the most likely explanation?
   A. Spark UI never shows streaming information on Databricks.
   B. The compute has no streaming job currently running.
   C. Auto Loader only works in batch, not streaming.
   D. The notebook is in SQL mode.

30. In classic file notification mode, which change is explicitly risky and can cause missed ingestion?
   A. Changing the output table name
   B. Changing the source path
   C. Adding a new filter transformation
   D. Increasing the cluster size

31. A pipeline must ingest millions of files per hour with low latency. Which mode is most likely recommended?
   A. Directory listing mode only
   B. File notification mode (or managed file events)
   C. Copying files into DBFS root and reading with `spark.read`
   D. Manual file enumeration in Python and `spark.createDataFrame`

32. Which statement best matches Auto Loader's source category?
   A. Auto Loader is designed for cloud object storage file ingestion.
   B. Auto Loader is designed to connect directly to relational databases via JDBC.
   C. Auto Loader is designed to query Delta tables incrementally.
   D. Auto Loader is designed to run SQL-only ingestion.

33. A data engineer wants observability into which files have been processed by an Auto Loader stream. Which Databricks feature helps return file-level state for an Auto Loader stream?
   A. `cloud_files_state(...)` table-valued function
   B. `DESCRIBE HISTORY` on the source path
   C. `EXPLAIN` on the query
   D. `VACUUM` on the target table

34. Which statement about using `display()` on a streaming DataFrame in a notebook is most accurate?
   A. `display()` cannot be used with streaming DataFrames.
   B. `display()` starts a streaming job for that streaming DataFrame.
   C. `display()` automatically writes to a Delta table with checkpointing.
   D. `display()` is the recommended production sink for streaming ingestion.

35. Which code fragment correctly initializes an Auto Loader read in PySpark?
   A. `spark.read.format("cloudFiles").load(path)`
   B. `spark.readStream.format("cloudFiles").load(path)`
   C. `spark.readStream.format("autoloader").load(path)`
   D. `spark.read.format("autoloader").load(path)`

36. When using Auto Loader to infer schema for JSON files, which option is used to store the inferred schema and track schema changes?
   A. `cloudFiles.checkpointLocation`
   B. `cloudFiles.schemaLocation`
   C. `cloudFiles.schema`
   D. `cloudFiles.inferSchemaOnly`

37. Which option specifies the file format (for example JSON) that Auto Loader should parse?
   A. `cloudFiles.format`
   B. `cloudFiles.fileType`
   C. `cloudFiles.sourceFormat`
   D. `cloudFiles.parser`

38. Which writeStream option is required to enable fault-tolerant progress tracking for a Structured Streaming query?
   A. `schemaLocation`
   B. `checkpointLocation`
   C. `mergeSchema`
   D. `queryName`

39. A team wants file ingestion to process all currently available files and then stop, behaving like a batch run while using streaming semantics. Which trigger is most appropriate?
   A. `.trigger(availableNow=True)`
   B. `.trigger(processingTime="0 seconds")`
   C. `.trigger(once=False)`
   D. `.trigger(batch=True)`

40. A data engineer uses Auto Loader and then writes to a Delta table with `.toTable("main.bronze.events")`. Which component most directly guarantees the stream can restart without reprocessing already committed data?
   A. Notebook version history
   B. `checkpointLocation`
   C. `%run` modularization
   D. Compute log delivery

41. Which of the following is the correct placement for the `checkpointLocation` setting in a typical Auto Loader pipeline?
   A. As a `cloudFiles.*` option on `readStream`
   B. As an option on `writeStream`
   C. As a parameter to `.load()`
   D. As a SQL comment in the notebook

42. A data engineer accidentally reuses the same checkpoint directory for two different streaming queries. What is the best answer?
   A. This is recommended to share progress and reduce costs.
   B. This is unsafe; each query should have a different checkpoint location.
   C. This only matters for Kafka, not for file sources.
   D. This only matters for batch jobs, not streaming.

43. A data engineer changes the Auto Loader source path but keeps the same checkpoint location. Which outcome is most consistent with Structured Streaming guidance?
   A. This is always safe and supported.
   B. Changing the input source generally requires a new checkpoint and can break restarts.
   C. Only the output sink matters; input changes do not matter.
   D. Spark will automatically migrate checkpoint state to the new path.

44. Which statement best distinguishes `cloudFiles.schemaLocation` from `checkpointLocation`?
   A. They are synonyms and always interchangeable.
   B. `schemaLocation` tracks schema inference/evolution; `checkpointLocation` tracks streaming progress/state.
   C. `schemaLocation` is only for SQL; `checkpointLocation` is only for Python.
   D. `schemaLocation` stores logs; `checkpointLocation` stores notebooks.

45. A Python notebook cell fails with a logic bug that only happens for certain variable values. Which built-in tool most directly supports stepping through code with breakpoints and inspecting variables?
   A. Interactive notebook debugger
   B. Spark UI Jobs page
   C. Compute log delivery
   D. Auto Loader file notification mode

46. During an interactive debug session, a user tries to run `display(df)` in the debug console to inspect data. What is the best expectation?
   A. `display` works normally in the debug console.
   B. `display` is not supported in the debug console; use `df.show()` or `df.head()` instead.
   C. `display` only works for SQL DataFrames, not PySpark.
   D. `display` runs on the workers only.

47. A Spark job fails, and the notebook output shows only a generic error. Where should you look first to see failed stages, tasks, and detailed failure context?
   A. Notebook version history
   B. Spark UI
   C. Widgets panel
   D. Git status dialog

48. A pipeline uses a cluster init script, but dependency installation fails intermittently. Which evidence source is most directly relevant?
   A. Auto Loader schema location files
   B. Init script stdout/stderr logs
   C. Notebook export HTML
   D. Version history diffs

49. A team wants logs preserved for analysis even after compute terminates. Which built-in capability best supports this?
   A. Notebook comments
   B. Compute log delivery to a configured destination
   C. `%md` documentation cells
   D. Widgets

50. A streaming query appears to be running but is not processing new data. Which built-in UI location is designed to monitor streaming progress and batches?
   A. Spark UI Streaming tab
   B. Notebook version history
   C. Import/export dialog
   D. Databricks Connect release notes
