# Section 2: Data Ingestion and Loading - Practice Exam

Exam guide version: NEW EXAM GUIDE, valid on or after 4-May-2026.

Question style: multiple-choice with four plausible options, modeled after the sample questions in the attached guide. These are original practice questions, not official exam questions.

## Questions

**Question 1**  
**Objective:** Enable and detail data ingestion patterns  
A data engineer has a one-time 20 MB CSV file on their laptop and wants to create a managed Delta table for exploration in Unity Catalog. Which ingestion method is most appropriate?

A. Use the Add Data UI to create or modify a table from file upload.  
B. Use Auto Loader with file notifications and a custom checkpoint.  
C. Build a Lakeflow Connect managed database connector.  
D. Use Lakehouse Federation because the data must not be copied.

**Question 2**  
**Objective:** Use `COPY INTO` to incrementally load files  
A SQL-focused team receives daily Parquet files in a Unity Catalog volume and wants an idempotent load into a bronze Delta table. Which command pattern should they use?

A. `COPY INTO catalog.schema.table FROM '/Volumes/catalog/schema/volume/path' FILEFORMAT = PARQUET`  
B. `INSERT INTO catalog.schema.table FROM '/Volumes/catalog/schema/volume/path' FILEFORMAT = PARQUET`  
C. `MERGE FILES INTO catalog.schema.table FROM '/Volumes/catalog/schema/volume/path'`  
D. `GRANT COPY INTO catalog.schema.table TO files`

**Question 3**  
**Objective:** Use `COPY INTO` to incrementally load files  
A `COPY INTO` command successfully loaded `orders_001.json` yesterday. The same command runs again today and the file is still in the source directory. What happens by default?

A. The file is skipped because `COPY INTO` tracks files already loaded.  
B. The file is loaded again because all source files are always reprocessed.  
C. The target table is dropped before the command runs.  
D. The command fails because `COPY INTO` cannot be rerun.

**Question 4**  
**Objective:** Use `COPY INTO` to incrementally load files  
A data engineer wants `COPY INTO` to load only files whose names begin with `orders_2026_05_`. Which clause should be used?

A. `PATTERN = '.*orders_2026_05_.*[.]json'`  
B. `WHERE file_name LIKE 'orders_2026_05_%'` before `FROM`  
C. `GROUP BY orders_2026_05`  
D. `USING PATTERN TABLE orders_2026_05`

**Question 5**  
**Objective:** Use `COPY INTO` to incrementally load files  
A team wants to load CSV files with a header row into a Delta table using SQL. Which option belongs in the `COPY INTO` command?

A. `FORMAT_OPTIONS ('header' = 'true')`  
B. `COPY_OPTIONS ('headerRow' = 'enabled')`  
C. `FILEFORMAT = HEADER`  
D. `WITH CSV_HEADER = TRUE`

**Question 6**  
**Objective:** Use `COPY INTO` to incrementally load files into Unity Catalog-governed tables  
A company wants governed file access for `COPY INTO` from cloud object storage. Which source path is most aligned with Unity Catalog governance?

A. A Unity Catalog volume path such as `/Volumes/main/raw/orders/` or a UC external location.  
B. A driver local disk path such as `/local_disk0/tmp/orders/`.  
C. A user's laptop path such as `C:\Downloads\orders`.  
D. A notebook markdown cell containing CSV rows.

**Question 7**  
**Objective:** Use Auto Loader with schema enforcement and schema evolution  
A data engineer wants to incrementally ingest new JSON files from a cloud storage landing path using Auto Loader. Which read format should they use?

A. `.format("cloudFiles")` with `.option("cloudFiles.format", "json")`  
B. `.format("delta")` with `.option("cloudFiles.format", "json")`  
C. `.format("jdbc")` with `.option("cloudFiles.format", "json")`  
D. `.format("json")` with `.option("copyInto", "true")`

**Question 8**  
**Objective:** Use Auto Loader with schema enforcement and schema evolution  
An Auto Loader job must infer schema and track schema changes over time. Which option is required for schema tracking?

A. `cloudFiles.schemaLocation`  
B. `spark.executor.instances`  
C. `delta.enableChangeDataFeed`  
D. `warehouseSize`

**Question 9**  
**Objective:** Use Auto Loader with schema enforcement and schema evolution  
A data engineer configures two independent Auto Loader streams to write to two different target tables. What is the correct checkpoint practice?

A. Use a separate checkpoint location for each stream.  
B. Use the same checkpoint location so both streams share file tracking state.  
C. Store checkpoints inside the source data directory so files are discovered faster.  
D. Disable checkpoints because Auto Loader does not need state.

**Question 10**  
**Objective:** Use Auto Loader in batch modes  
A pipeline should process all currently available files and then stop, while preserving incremental state for the next run. Which trigger is most appropriate?

A. `.trigger(availableNow=True)`  
B. `.trigger(oncePerExecutor=True)`  
C. `.trigger(dropFiles=True)`  
D. `.trigger(copyInto=True)`

**Question 11**  
**Objective:** Use Auto Loader with directory listing or file notification  
A simple low-volume landing zone does not require cloud notification configuration. Which Auto Loader discovery mode is the most straightforward starting point?

A. Directory listing  
B. JDBC federation  
C. Delta Sharing recipient polling  
D. SQL warehouse result caching

**Question 12**  
**Objective:** Use Auto Loader with directory listing or file notification  
A high-volume landing path needs more efficient file discovery using cloud file event metadata. Which Auto Loader option is most relevant in current Unity Catalog-oriented patterns?

A. `cloudFiles.useManagedFileEvents`  
B. `copyInto.skipEvents`  
C. `delta.autoMergeNotifications`  
D. `jdbc.notificationMode`

**Question 13**  
**Objective:** Use Auto Loader with schema enforcement and schema evolution  
An Auto Loader stream has no explicit schema and uses the default schema evolution mode. A new column appears in incoming JSON. What should the engineer expect?

A. Auto Loader can add the column to schema metadata and fail the stream so it can restart with the evolved schema.  
B. Auto Loader always drops the new column silently.  
C. Auto Loader converts the entire table to a SQL warehouse.  
D. Auto Loader disables Unity Catalog permissions.

**Question 14**  
**Objective:** Use Auto Loader with schema enforcement and schema evolution  
A data engineer wants unexpected JSON fields to be preserved instead of lost, without automatically adding every new field as a top-level table column. Which Auto Loader setting is most appropriate?

A. Use schema evolution mode `rescue` with a rescued data column.  
B. Use `COPY INTO` with `FILEFORMAT = JDBC`.  
C. Use a SQL warehouse with auto-stop disabled.  
D. Use a local file upload and manually ignore bad fields.

**Question 15**  
**Objective:** Use Auto Loader with schema enforcement and schema evolution  
A data engineer wants Auto Loader to infer a field as a specific type or name casing without fully providing a fixed schema. Which option should they use?

A. `cloudFiles.schemaHints`  
B. `cloudFiles.disableSchema`  
C. `COPY_OPTIONS ('checkpoint' = 'true')`  
D. `spark.sql.shuffle.partitions`

**Question 16**  
**Objective:** Use Auto Loader with schema enforcement and schema evolution  
A source sends malformed JSON records and occasional unexpected fields. The team wants to retain unexpected fields for later analysis. Which concept should they use?

A. Rescued data column  
B. Account billing export  
C. Cluster policy family  
D. SQL dashboard parameter

**Question 17**  
**Objective:** Configure Lakeflow Connect for enterprise ingestion  
A company wants to ingest from a supported enterprise application with minimal code, managed authentication, retries, and schema evolution. Which option should be considered first?

A. Lakeflow Connect managed connector  
B. Manual local file upload every morning  
C. A custom parser running on a laptop  
D. Lakehouse Federation, because it copies data into Delta tables

**Question 18**  
**Objective:** Configure Lakeflow Connect for enterprise ingestion  
A source database is supported by a Lakeflow Connect managed connector and the team wants CDC-style incremental replication into Unity Catalog-governed Delta tables. Which pattern is the best fit?

A. Configure the managed database connector and target catalog/schema.  
B. Upload exported CSV files through the UI once per quarter.  
C. Query the database in place with Lakehouse Federation and call it ingestion.  
D. Store database credentials in notebook markdown.

**Question 19**  
**Objective:** Prioritize between ingestion methods  
A team needs full customization over a Kafka stream and wants to define custom parsing and transformation logic in a pipeline. Which option is most appropriate?

A. Lakeflow Connect standard connector or Structured Streaming pattern for Kafka.  
B. Local file upload.  
C. `COPY INTO` from a notebook cell.  
D. Unity Catalog table grants only.

**Question 20**  
**Objective:** Prioritize between ingestion methods  
A data engineer is choosing among ingestion options. The source is supported by a fully managed connector and no special customization is required. What principle should guide the choice?

A. Start with the most managed option that satisfies the requirements.  
B. Always write a custom REST client first.  
C. Always avoid Unity Catalog for ingestion.  
D. Always use a local CSV export even for supported sources.

**Question 21**  
**Objective:** Prioritize between ingestion methods  
A company already uses a validated third-party ingestion tool for a SaaS platform that Databricks managed connectors do not support. Which option is reasonable?

A. Use a partner connector or third-party integration if it meets governance and operational requirements.  
B. Use `COPY INTO` directly against the SaaS API URL.  
C. Use Lakehouse Federation to automatically copy all SaaS records.  
D. Upload screenshots of the SaaS application to a Delta table.

**Question 22**  
**Objective:** Prioritize between ingestion methods  
A team only needs to query a few tables in an external database without copying them into Databricks. Which technology is the better conceptual fit?

A. Lakehouse Federation  
B. Auto Loader  
C. `COPY INTO`  
D. Local file upload

**Question 23**  
**Objective:** Use JDBC/ODBC or REST clients in notebooks  
A notebook reads from an external PostgreSQL database using JDBC. Where should the username and password be stored?

A. In Databricks secrets or a governed connection pattern, not hard-coded in the notebook.  
B. In a markdown cell at the top of the notebook.  
C. In the target Delta table comment.  
D. In the Spark UI stage name.

**Question 24**  
**Objective:** Use JDBC/ODBC or REST clients in notebooks  
A REST API returns changed customer records using an `updated_after` parameter. The ingestion notebook should avoid reloading all records every run. Which approach is best?

A. Store and use a watermark such as the maximum `updated_at` loaded.  
B. Disable all target table constraints.  
C. Always truncate the bronze table and call every API page from the beginning.  
D. Use a SQL warehouse size as the API cursor.

**Question 25**  
**Objective:** Use JDBC/ODBC or REST clients in notebooks  
A team wants an immutable raw copy of each REST API response before parsing it into bronze tables. Which pattern is most appropriate?

A. Write raw JSON responses to a Unity Catalog volume, then ingest or parse into Delta tables.  
B. Store the JSON only in notebook output.  
C. Convert the JSON to screenshots.  
D. Put the API token in the target table name.

**Question 26**  
**Objective:** Use JDBC/ODBC or REST clients in notebooks  
A JDBC notebook uses many parallel partitions to read from a small operational database and the source database becomes unstable. What is the likely issue?

A. Too many parallel JDBC reads can overload the source system.  
B. Unity Catalog cannot govern JDBC results.  
C. `COPY INTO` automatically deleted the database.  
D. JSON schema evolution caused disk spilling in the source database.

**Question 27**  
**Objective:** Enable and detail data ingestion patterns  
Which statement correctly distinguishes ingestion from Lakehouse Federation?

A. Ingestion copies data into Databricks tables or storage; federation queries external data in place.  
B. Federation is the only way to create Delta tables.  
C. Ingestion never copies data.  
D. `COPY INTO` is a query federation connector.

**Question 28**  
**Objective:** Ingest semi-structured and unstructured data  
A source emits highly variable JSON payloads with many optional and changing fields. The team wants to preserve the raw flexible structure in a Delta table on a supported runtime. Which data type can be appropriate?

A. `VARIANT`  
B. `BINARY AS CLUSTER`  
C. `WAREHOUSE`  
D. `CHECKPOINT`

**Question 29**  
**Objective:** Ingest semi-structured and unstructured data  
A source emits stable JSON with known fields. The team wants strongly typed columns for downstream silver transformations. Which approach is best?

A. Read JSON with an explicit schema or infer then enforce a schema before writing a Delta table.  
B. Store every record as an unparsed screenshot.  
C. Use only local file upload and avoid Delta tables.  
D. Put all fields in the table name.

**Question 30**  
**Objective:** Ingest semi-structured and unstructured data  
A team uploads PDFs and image files for later AI processing and wants governed file access. Which Unity Catalog object is most appropriate?

A. Volume  
B. SQL warehouse  
C. Delta table primary key  
D. Cluster event log

**Question 31**  
**Objective:** Enable and detail data ingestion patterns  
A data engineer uploads several CSV files through the create/modify table UI. What operation does the UI perform when combining multiple uploaded files into one table?

A. It appends rows from the files; it does not join or merge records.  
B. It automatically joins files by primary key.  
C. It creates a Lakeflow Connect managed connector.  
D. It converts every CSV into a SQL dashboard.

**Question 32**  
**Objective:** Enable and detail data ingestion patterns  
A recurring ingestion workflow processes files that arrive unpredictably throughout the day. A time-based schedule would run too often when no files arrive. Which Lakeflow Jobs trigger concept can help start runs when new files arrive?

A. File arrival trigger  
B. Manual-only trigger  
C. Git branch trigger  
D. SQL comment trigger

**Question 33**  
**Objective:** Enable and detail data ingestion patterns  
A downstream transformation should run only after its source Delta table has been updated. Which Lakeflow Jobs trigger concept is most relevant?

A. Table update trigger  
B. Local file upload trigger  
C. Notebook markdown trigger  
D. Account billing trigger

**Question 34**  
**Objective:** Use `COPY INTO` to incrementally load files  
A data engineer wants to check which rows would be loaded by a `COPY INTO` operation before performing the load. Which clause is relevant?

A. `VALIDATE`  
B. `DRY_CLUSTER`  
C. `REVOKE LOAD`  
D. `LIST NOTEBOOKS`

**Question 35**  
**Objective:** Use `COPY INTO` to incrementally load files  
A team wants to load files from S3, ADLS, or GCS into a Delta table using SQL. Which statement is correct?

A. `COPY INTO` supports loading from cloud object storage locations into Delta tables.  
B. `COPY INTO` can only load from JDBC databases.  
C. `COPY INTO` can only be used with local laptop files.  
D. `COPY INTO` is only for granting Unity Catalog privileges.

**Question 36**  
**Objective:** Use Auto Loader with schema enforcement and schema evolution  
A data engineer stores Auto Loader checkpoint files in the same folder where source JSON files arrive. What is the main problem?

A. Checkpoint files can be discovered as source data or interfere with clean source separation; use a separate checkpoint location.  
B. Auto Loader requires checkpoints to be stored in the target table name.  
C. Checkpoints must always be stored in the Databricks control plane.  
D. Checkpoints are not used by Auto Loader.

**Question 37**  
**Objective:** Configure Lakeflow Connect for enterprise ingestion  
Which privilege is commonly needed to create a Unity Catalog connection for a managed ingestion source?

A. `CREATE CONNECTION` on the metastore  
B. `SELECT` on every notebook  
C. `MODIFY` on the Spark UI  
D. `DELETE` on the Databricks account

**Question 38**  
**Objective:** Configure Lakeflow Connect for enterprise ingestion  
A non-admin user wants to create a Lakeflow Connect managed ingestion pipeline through APIs using an existing source connection. Which permission is commonly required on that connection?

A. `USE CONNECTION`  
B. `USE NOTEBOOK CELL`  
C. `MANAGE SPARK STAGE`  
D. `SELECT CHECKPOINT`

**Question 39**  
**Objective:** Prioritize between ingestion methods  
A company needs to ingest millions of small cloud files per hour with incremental discovery and exactly-once style file processing. Which tool is the best starting point?

A. Auto Loader  
B. Manual file upload  
C. Notebook markdown import  
D. Account console export

**Question 40**  
**Objective:** Prioritize between ingestion methods  
A team has only a few thousand files and wants a simple SQL command scheduled nightly. Which tool is likely sufficient?

A. `COPY INTO`  
B. A custom Kafka streaming connector  
C. Lakehouse Federation only  
D. Manual copy-paste into a notebook

**Question 41**  
**Objective:** Ingest semi-structured and unstructured data  
A data engineer needs to query nested fields from a JSON-like `VARIANT` column. Which syntax pattern is appropriate?

A. `raw:customer.id`  
B. `raw CUSTOMER JOIN id`  
C. `raw->warehouse->id` as a compute setting  
D. `SELECT CLUSTER(raw.customer.id)`

**Question 42**  
**Objective:** Ingest semi-structured and unstructured data  
A JSON ingestion pipeline sees new optional fields in incoming payloads. The team wants these fields available later without losing the original record. Which feature helps protect against data loss?

A. Auto Loader rescued data column  
B. SQL warehouse auto-stop  
C. Git pull request comments  
D. Account-level billing filter

**Question 43**  
**Objective:** Use JDBC/ODBC or REST clients in notebooks  
A REST API uses pagination. The notebook only fetches the first page and writes it to bronze. What is the likely problem?

A. The ingestion is incomplete because remaining pages were not requested.  
B. Auto Loader will automatically fetch the remaining API pages.  
C. Unity Catalog prevents paginated APIs.  
D. `COPY INTO` will backfill REST pages automatically.

**Question 44**  
**Objective:** Use JDBC/ODBC or REST clients in notebooks  
A REST API returns HTTP 429 rate limit responses. Which notebook ingestion behavior is most appropriate?

A. Implement retry and backoff logic or use a managed connector if available.  
B. Ignore all HTTP status codes and write empty rows.  
C. Increase SQL warehouse size to bypass API limits.  
D. Store the API password in plain text to avoid rate limits.

**Question 45**  
**Objective:** Prioritize between ingestion methods  
A team wants governed ingestion into Unity Catalog tables and requires source-specific CDC, authentication management, and automated retries for a supported database. Which choice best satisfies the requirements?

A. Lakeflow Connect managed connector  
B. `COPY INTO` from a local text file  
C. Manual REST loop without checkpoints  
D. Lakehouse Federation only

**Question 46**  
**Objective:** Use Auto Loader with schema enforcement and schema evolution  
A data engineer wants to load binary files from a volume and track file path, modification time, and content for processing. Which Auto Loader or Spark file format concept is relevant?

A. `binaryFile`  
B. `jdbcFile`  
C. `warehouseFile`  
D. `grantFile`

**Question 47**  
**Objective:** Enable and detail data ingestion patterns  
Which ingestion pattern is best described as "processing only source records or files that are new or changed since the last successful run"?

A. Incremental loading  
B. Full refresh every time  
C. Local-only upload  
D. Static dashboarding

**Question 48**  
**Objective:** Use `COPY INTO` to incrementally load files  
A data engineer writes the following command:

```sql
COPY INTO de_academy.bronze.orders
FROM '/Volumes/de_academy/raw/orders/'
FILEFORMAT = JSON
COPY_OPTIONS ('mergeSchema' = 'true');
```

What is the main intent of `mergeSchema` in this context?

A. Allow schema changes such as new columns to be merged where supported.  
B. Merge the source files by joining them on a primary key.  
C. Merge the target catalog with another workspace.  
D. Merge all job runs into a single cluster.

**Question 49**  
**Objective:** Prioritize between ingestion methods  
A team wants to ingest from a source that has no managed connector, but open-source libraries can call its API. The pipeline needs custom authentication and pagination logic. Which option fits best?

A. Custom notebook or application ingestion using REST/API libraries, orchestrated by Lakeflow Jobs.  
B. Local UI upload of one sample file every day.  
C. `COPY INTO` directly from the API endpoint.  
D. Unity Catalog grants only, with no ingestion code.

**Question 50**  
**Objective:** Prioritize between ingestion methods  
A team has four sources: small one-time CSV, nightly cloud object storage files, continuous high-volume file arrivals with schema drift, and a supported enterprise SaaS source. Which mapping is best?

A. File upload for the small CSV; `COPY INTO` for nightly files; Auto Loader for high-volume schema-drifting files; Lakeflow Connect managed connector for supported SaaS.  
B. Auto Loader for the small CSV; local file upload for high-volume files; Lakehouse Federation for SaaS CDC; notebook markdown for nightly files.  
C. `COPY INTO` for every source, including SaaS APIs and JDBC databases.  
D. Lakehouse Federation for all sources because ingestion always avoids copying data.

## Answer Key

**Question 1: A**  
A is correct because local file upload is appropriate for small, one-time exploratory data. B is overbuilt for a 20 MB manual file. C is for supported enterprise/database ingestion, not a one-time laptop CSV. D is wrong because Lakehouse Federation queries external data in place and is not needed for a local file import.

**Question 2: A**  
A is correct because `COPY INTO` loads files from a location into a Delta table and is a SQL-friendly idempotent pattern. B is not valid `INSERT INTO` syntax for loading files. C is not a Databricks SQL ingestion command. D confuses ingestion with permissions.

**Question 3: A**  
A is correct because `COPY INTO` skips files already loaded by that command pattern. B is wrong because the default behavior is not to blindly reload processed files. C and D are unrelated to `COPY INTO` behavior.

**Question 4: A**  
A is correct because `PATTERN` filters source files by a glob or regex-like pattern in `COPY INTO`. B uses an invalid position and concept for file name filtering. C aggregates data and does not filter files. D is not valid syntax.

**Question 5: A**  
A is correct because CSV reader options such as `header` belong in `FORMAT_OPTIONS`. B, C, and D are not valid ways to express the CSV header option in `COPY INTO`.

**Question 6: A**  
A is correct because Unity Catalog volumes and external locations provide governed cloud storage access. B is local to the driver and not a governed cloud landing zone. C is not directly accessible to Databricks SQL. D is not a file storage location.

**Question 7: A**  
A is correct because Auto Loader uses the `cloudFiles` source and a `cloudFiles.format` option for the underlying file format. B reads a Delta table, not Auto Loader files. C is JDBC, not file ingestion. D is a basic JSON file read, not Auto Loader incremental discovery.

**Question 8: A**  
A is correct because `cloudFiles.schemaLocation` stores Auto Loader schema inference and evolution metadata. B tunes Spark execution. C enables Delta change data feed, not Auto Loader schema tracking. D is SQL warehouse sizing.

**Question 9: A**  
A is correct because each independent stream needs its own checkpoint state. B can corrupt or mix stream progress. C risks source/checkpoint interference. D is false because Auto Loader uses streaming state.

**Question 10: A**  
A is correct because `availableNow` processes available data and then stops while preserving incremental state. B, C, and D are not valid trigger options for this purpose.

**Question 11: A**  
A is correct because directory listing is the simplest default discovery mode. B is database connectivity. C is data sharing. D is query caching, not file discovery.

**Question 12: A**  
A is correct because managed file events are used for efficient event-based discovery in modern UC-oriented patterns. B, C, and D are not valid Auto Loader file event options.

**Question 13: A**  
A is correct because the default `addNewColumns` behavior can update schema metadata and fail the stream so it restarts with the new schema. B describes a different mode. C and D are unrelated.

**Question 14: A**  
A is correct because rescue mode and rescued data columns preserve unexpected fields without top-level schema evolution. B is invalid because `COPY INTO` does not use `FILEFORMAT = JDBC`. C is compute configuration. D is manual and not reliable.

**Question 15: A**  
A is correct because schema hints guide inference without fully fixing the stream schema. B is not a valid option. C confuses `COPY INTO` and checkpointing. D tunes shuffle partitions, not schema inference.

**Question 16: A**  
A is correct because a rescued data column stores unexpected or mismatched fields for later analysis. B, C, and D are unrelated to malformed or drifting JSON fields.

**Question 17: A**  
A is correct because managed connectors are designed for supported enterprise sources with low-code operations, retries, authentication handling, and schema management. B and C are manual and fragile. D is wrong because Lakehouse Federation does not copy into Delta tables.

**Question 18: A**  
A is correct because a managed database connector is the best fit for supported CDC/incremental database ingestion into UC-governed tables. B is not timely or production-grade. C is not ingestion. D is insecure.

**Question 19: A**  
A is correct because Kafka and similar message bus sources usually require standard connector or Structured Streaming patterns when custom logic is needed. B and C are file patterns, not Kafka ingestion. D grants permissions but does not ingest.

**Question 20: A**  
A is correct because Databricks recommends starting with the most managed option that satisfies requirements. B creates unnecessary custom maintenance. C is wrong because Unity Catalog governance is central to modern ingestion. D is inefficient and manual.

**Question 21: A**  
A is correct because partner connectors are reasonable when managed connectors do not support the source and the partner integration satisfies governance and operations. B is wrong because `COPY INTO` loads files, not SaaS API URLs. C queries in place rather than ingesting. D is nonsensical.

**Question 22: A**  
A is correct because Lakehouse Federation queries external data without copying it. B and C ingest files into Databricks. D is for local manual uploads.

**Question 23: A**  
A is correct because credentials should be stored securely in secrets or governed connection objects. B and C expose secrets. D is execution metadata and cannot securely store credentials.

**Question 24: A**  
A is correct because a watermark allows incremental API extraction. B has no effect on source extraction. C is wasteful and risks API overload. D is unrelated to API cursoring.

**Question 25: A**  
A is correct because storing raw JSON in a governed volume preserves source responses and supports reprocessing. B is not durable. C loses machine-readable structure. D exposes secrets and is not ingestion design.

**Question 26: A**  
A is correct because too much JDBC parallelism can overload source databases. B is too broad and false. C and D are unrelated.

**Question 27: A**  
A is correct because ingestion copies data into Databricks, while federation queries data in place. B, C, and D incorrectly define federation or ingestion.

**Question 28: A**  
A is correct because `VARIANT` can store flexible semi-structured data on supported runtimes. B, C, and D are not valid data types for this purpose.

**Question 29: A**  
A is correct because stable JSON should be parsed into typed columns for downstream reliability. B, C, and D do not support useful analytics or governance.

**Question 30: A**  
A is correct because Unity Catalog volumes govern non-tabular files such as PDFs and images. B runs SQL. C is a table modeling concept. D is compute diagnostics.

**Question 31: A**  
A is correct because multi-file upload appends rows and does not join or merge records. B is unsupported. C and D are unrelated to file upload behavior.

**Question 32: A**  
A is correct because file arrival triggers can run a job when new files arrive in a monitored UC storage location. B requires manual runs. C and D are not Lakeflow Jobs trigger types.

**Question 33: A**  
A is correct because table update triggers respond to changes in supported source tables. B, C, and D are not relevant trigger types for table updates.

**Question 34: A**  
A is correct because `VALIDATE` is the relevant `COPY INTO` clause for validating rows before loading. B, C, and D are not valid `COPY INTO` validation clauses.

**Question 35: A**  
A is correct because `COPY INTO` supports loading files from cloud object storage into Delta tables. B and C incorrectly restrict the source. D confuses ingestion with privileges.

**Question 36: A**  
A is correct because source and checkpoint paths should be separated to prevent interference and preserve clean state. B and C are false. D is false because Auto Loader uses checkpoints.

**Question 37: A**  
A is correct because creating Unity Catalog connections commonly requires `CREATE CONNECTION` on the metastore. B, C, and D are unrelated privileges.

**Question 38: A**  
A is correct because using an existing UC connection requires `USE CONNECTION` or sufficient privileges. B, C, and D are not valid permission concepts.

**Question 39: A**  
A is correct because Auto Loader is designed for scalable incremental file ingestion. B and C are manual and not scalable. D is administrative, not ingestion.

**Question 40: A**  
A is correct because `COPY INTO` is a simple SQL fit for scheduled file loads at modest scale. B is unnecessary for simple files. C does not ingest. D is manual and error-prone.

**Question 41: A**  
A is correct because `:` path syntax is used to query fields in semi-structured/variant data. B, C, and D are invalid for querying nested fields.

**Question 42: A**  
A is correct because the rescued data column preserves unexpected fields for later analysis. B, C, and D do not prevent semi-structured data loss.

**Question 43: A**  
A is correct because paginated APIs require fetching all relevant pages. B and D are false because Auto Loader and `COPY INTO` do not automatically call REST pages. C is false.

**Question 44: A**  
A is correct because rate-limited APIs require retry/backoff handling, or a managed connector if available. B loses data silently. C does not override source API limits. D is insecure and unrelated.

**Question 45: A**  
A is correct because managed connectors provide source-specific CDC, authentication handling, retries, and managed ingestion for supported databases. B, C, and D do not satisfy the stated managed CDC ingestion requirement.

**Question 46: A**  
A is correct because `binaryFile` is the relevant format for loading binary file metadata and content. B, C, and D are not valid file ingestion formats.

**Question 47: A**  
A is correct because incremental loading processes only new or changed data since prior successful processing. B is the opposite. C and D are unrelated.

**Question 48: A**  
A is correct because `mergeSchema` allows compatible schema evolution such as new columns where supported. B confuses schema merging with relational joins. C and D are unrelated.

**Question 49: A**  
A is correct because unsupported APIs with custom pagination and authentication often require custom notebook/application logic orchestrated by Lakeflow Jobs. B is manual and incomplete. C is invalid because `COPY INTO` loads files, not arbitrary API endpoints. D grants access but does not ingest.

**Question 50: A**  
A is correct because each source maps to the most appropriate ingestion method: manual upload for small one-time files, `COPY INTO` for simple nightly file loads, Auto Loader for high-volume drifting files, and Lakeflow Connect managed connectors for supported SaaS. B, C, and D mismatch tools to source types and operational needs.

