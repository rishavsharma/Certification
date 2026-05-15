# Section 2: Data Ingestion and Loading - Learning Guide

Exam guide version: NEW EXAM GUIDE, valid on or after 4-May-2026.

## Section Objectives

This section covers seven syllabus bullets from the NEW EXAM GUIDE:

1. Enable and detail data ingestion patterns, including batch, streaming, and incremental loading, and import data from sources such as local files, Lakeflow Connect standard connectors, and Lakeflow Connect managed connectors.
2. Use the `COPY INTO` command to incrementally load files from cloud object storage (ADLS/S3/GCS) into Unity Catalog-governed tables.
3. Use Auto Loader with schema enforcement and schema evolution in batch modes, for example directory listing or file notification, to land data into Unity Catalog-governed tables.
4. Configure Lakeflow Connect to reliably ingest data from diverse enterprise sources into Unity Catalog-governed tables.
5. Use JDBC/ODBC or REST clients in notebooks to land data into cloud storage or directly into Unity Catalog-governed tables, usually orchestrated and scheduled with Lakeflow Jobs.
6. Prioritize between Auto Loader, Lakeflow Connect standard and managed connectors, partner connectors, and other ingestion methods based on technical requirements such as data volume, ingestion frequency, data types, and governance needs with Unity Catalog.
7. Ingest semi-structured and unstructured data, for example JSON and nested data, via Lakeflow Connect and other managed connectors into Unity Catalog-governed Delta tables.

## Exam Mental Model

Ingestion means moving data from a source system into Databricks storage or tables so downstream transformations can run reliably. For the Associate exam, ingestion is mostly about choosing the right tool:

- Local file upload: small, manual, exploratory ingestion.
- `COPY INTO`: SQL-friendly incremental file loading from cloud object storage or Unity Catalog volumes into Delta tables.
- Auto Loader: scalable incremental file ingestion from cloud object storage using the `cloudFiles` source.
- Lakeflow Connect managed connectors: low-operations ingestion from supported enterprise applications and databases.
- Lakeflow Connect standard connectors: more customizable ingestion from cloud object storage, message buses, and other supported sources.
- Partner connectors: use validated third-party tools when source support, reverse ETL, SaaS integration, or vendor-specific features matter.
- Notebook-based JDBC/ODBC/REST: custom ingestion logic when managed patterns do not fit, usually scheduled by Lakeflow Jobs.

The exam often gives a scenario with source type, data volume, frequency, schema drift, governance, or operational requirements. Pick the simplest managed tool that satisfies the constraints.

## Ingestion Patterns

### Batch Loading

Batch loading processes data at planned intervals. The source might produce hourly files, daily extracts, or occasional manual uploads.

Common Databricks patterns:

- Local file upload for small manual files.
- `COPY INTO` for scheduled SQL-based file ingestion.
- Auto Loader with `trigger(availableNow=True)` for incremental batch-style file processing.
- Lakeflow Connect managed connectors scheduled through Lakeflow Jobs.

Example batch-style Auto Loader:

```python
source_path = "/Volumes/de_academy/raw/orders_dropzone"
checkpoint_path = "/Volumes/de_academy/raw/_checkpoints/orders_autoloader"
schema_path = "/Volumes/de_academy/raw/_schemas/orders_autoloader"

(
    spark.readStream
        .format("cloudFiles")
        .option("cloudFiles.format", "json")
        .option("cloudFiles.schemaLocation", schema_path)
        .load(source_path)
        .writeStream
        .option("checkpointLocation", checkpoint_path)
        .trigger(availableNow=True)
        .toTable("de_academy.bronze.orders_raw")
)
```

Exam focus:

- Batch does not always mean "one-time." Scheduled batch loads can be repeated.
- `AvailableNow` is a common way to process all currently available files and then stop.
- Checkpointing is still required for incremental file processing.

### Streaming Loading

Streaming loading processes data as it arrives or in frequent micro-batches. In Databricks, Auto Loader and Structured Streaming patterns are common.

Example continuous Auto Loader pattern:

```python
(
    spark.readStream
        .format("cloudFiles")
        .option("cloudFiles.format", "json")
        .option("cloudFiles.schemaLocation", "/Volumes/de_academy/raw/_schemas/clicks")
        .load("/Volumes/de_academy/raw/clickstream")
        .writeStream
        .option("checkpointLocation", "/Volumes/de_academy/raw/_checkpoints/clicks")
        .toTable("de_academy.bronze.clickstream_raw")
)
```

Exam focus:

- Streaming ingestion needs checkpointing.
- Streaming is a good fit for event data, logs, IoT, message buses, and near-real-time use cases.
- Lakeflow Spark Declarative Pipelines and Lakeflow Jobs can manage streaming and triggered pipelines.

### Incremental Loading

Incremental loading processes only new or changed data since a previous successful run.

Common examples:

- `COPY INTO` skips files already loaded into the target table.
- Auto Loader tracks discovered files using checkpoints.
- Lakeflow Connect managed database connectors can use CDC or source-specific incremental logic where supported.
- REST or JDBC notebook ingestion can use watermarks, timestamps, IDs, or source change tokens.

Example watermark in a REST notebook:

```python
from datetime import datetime, timezone
import requests

last_watermark = spark.sql("""
  SELECT coalesce(max(updated_at), timestamp('1970-01-01')) AS watermark
  FROM de_academy.bronze.crm_accounts
""").first()["watermark"]

token = dbutils.secrets.get("crm_scope", "api_token")
response = requests.get(
    "<crm-api-url>/accounts",
    headers={"Authorization": f"Bearer {token}"},
    params={"updated_after": last_watermark.isoformat()},
    timeout=60,
)
response.raise_for_status()

accounts_df = spark.createDataFrame(response.json()["accounts"])
(
    accounts_df.write
        .format("delta")
        .mode("append")
        .saveAsTable("de_academy.bronze.crm_accounts")
)
```

Exam focus:

- Incremental loading requires state: file tracking, checkpoints, CDC metadata, or watermarks.
- Do not reprocess every file or every source row unless the scenario asks for full refresh.

## Local File Import

Databricks supports local file upload through the Add Data UI. This is useful for learning, demos, small sample datasets, or one-off table creation.

Use cases:

- Upload a small CSV/JSON/Parquet/Avro/text sample.
- Create or overwrite a managed Delta table from a file.
- Upload non-tabular files to a Unity Catalog volume for later processing.

Limitations to remember:

- Local upload is not a production-scale ingestion method.
- The UI has file count and size limits.
- Compressed archives such as zip and tar are not supported for the create/modify table UI.
- Uploading multiple files appends rows; it does not join or merge records.

Exam selection rule:

Choose local file upload only for small, manual, exploratory data. For recurring, large, governed ingestion, choose `COPY INTO`, Auto Loader, Lakeflow Connect, or a partner/standard connector.

## `COPY INTO`

`COPY INTO` is a SQL command for loading files into a Delta table. It is retryable and idempotent: files already loaded from the source location are skipped.

Best fit:

- SQL-first teams.
- Thousands of files rather than extremely high file volumes.
- Scheduled incremental file loads from S3, ADLS, GCS, external locations, or Unity Catalog volumes.
- Simple file ingestion where Auto Loader would be more than needed.

Not the best fit:

- Very high-scale file ingestion with millions or billions of files.
- Near-real-time file discovery.
- Complex schema drift handling at scale.
- Sources that are not files.

### Basic `COPY INTO` Example

```sql
CREATE CATALOG IF NOT EXISTS de_academy;
CREATE SCHEMA IF NOT EXISTS de_academy.bronze;

CREATE TABLE IF NOT EXISTS de_academy.bronze.orders_raw (
  order_id STRING,
  customer_id STRING,
  order_ts TIMESTAMP,
  amount DECIMAL(10, 2)
)
USING DELTA;

COPY INTO de_academy.bronze.orders_raw
FROM '/Volumes/de_academy/raw/orders_landing/'
FILEFORMAT = JSON
FORMAT_OPTIONS (
  'inferTimestamp' = 'true'
);
```

### `COPY INTO` with a Pattern

```sql
COPY INTO de_academy.bronze.orders_raw
FROM '/Volumes/de_academy/raw/orders_landing/'
FILEFORMAT = JSON
PATTERN = '.*orders_2026_05_.*[.]json'
FORMAT_OPTIONS (
  'multiline' = 'false'
);
```

### `COPY INTO` with Schema Evolution

```sql
CREATE TABLE IF NOT EXISTS de_academy.bronze.web_events_raw;

COPY INTO de_academy.bronze.web_events_raw
FROM '/Volumes/de_academy/raw/web_events/'
FILEFORMAT = JSON
FORMAT_OPTIONS (
  'mergeSchema' = 'true'
)
COPY_OPTIONS (
  'mergeSchema' = 'true'
);
```

Exam focus:

- `COPY INTO` loads from a file location into a Delta table.
- It is idempotent for files it has already loaded.
- It can use `FILES`, `PATTERN`, `FORMAT_OPTIONS`, and `COPY_OPTIONS`.
- It can target Unity Catalog-governed Delta tables.
- Unity Catalog volumes or external locations are preferred for governed cloud storage access.

## Auto Loader

Auto Loader incrementally and efficiently processes new files as they arrive in cloud storage. It uses the `cloudFiles` source and Structured Streaming under the hood.

Best fit:

- Large-scale file ingestion.
- Frequent new files.
- Incremental ingestion with checkpointing.
- Schema inference and schema evolution.
- JSON, CSV, XML, Avro, Parquet, text, and binary file ingestion patterns, subject to runtime support.
- Billions of files or high-throughput file discovery use cases.

### Directory Listing vs File Notification

Auto Loader can discover files through:

- Directory listing: scans the source directory to discover files. This is supported by default and is simpler to configure.
- File notification or managed file events: uses cloud file event metadata to detect new files more efficiently. This is better for high-scale or low-latency discovery, but has additional configuration and access requirements.

For Unity Catalog workloads, checkpoint and schema locations should be in UC-governed storage such as volumes or external locations.

### Auto Loader with Directory Listing

```python
(
    spark.readStream
        .format("cloudFiles")
        .option("cloudFiles.format", "csv")
        .option("header", "true")
        .option("cloudFiles.schemaLocation", "/Volumes/de_academy/raw/_schemas/products")
        .load("/Volumes/de_academy/raw/products_landing")
        .writeStream
        .option("checkpointLocation", "/Volumes/de_academy/raw/_checkpoints/products")
        .trigger(availableNow=True)
        .toTable("de_academy.bronze.products_raw")
)
```

### Auto Loader with Managed File Events

```python
(
    spark.readStream
        .format("cloudFiles")
        .option("cloudFiles.format", "json")
        .option("cloudFiles.useManagedFileEvents", "true")
        .option("cloudFiles.schemaLocation", "/Volumes/de_academy/raw/_schemas/iot_events")
        .load("/Volumes/de_academy/raw/iot_events")
        .writeStream
        .option("checkpointLocation", "/Volumes/de_academy/raw/_checkpoints/iot_events")
        .trigger(availableNow=True)
        .toTable("de_academy.bronze.iot_events_raw")
)
```

Exam focus:

- File notification/managed file events are used for more efficient discovery.
- Directory listing is simpler and works by default.
- Checkpoint and schema locations must be separate from the source data path.
- One ingestion stream should have its own checkpoint.

### Schema Enforcement and Schema Evolution

Schema enforcement means incoming data must conform to the expected schema. Schema evolution means the ingestion process can adapt to allowed schema changes.

Auto Loader tracks schema metadata in the `cloudFiles.schemaLocation`.

Common options:

- `cloudFiles.schemaLocation`: location where Auto Loader stores inferred schemas.
- `cloudFiles.schemaEvolutionMode`: controls behavior when new columns appear.
- `cloudFiles.schemaHints`: gives hints to inference without fully fixing the schema.
- `rescuedDataColumn`: captures unexpected or problematic fields in a rescue column.

Schema evolution modes to recognize:

| Mode | Behavior |
| --- | --- |
| `addNewColumns` | Default when no schema is provided. New columns are added to the schema, and the stream fails so it can restart with the new schema. |
| `rescue` | New or mismatched fields are stored in the rescued data column and the schema is not evolved. |
| `failOnNewColumns` | Fails when new columns appear until the schema or source files are corrected. |
| `none` | Does not evolve the schema. New columns are ignored unless rescued data is configured. |
| `addNewColumnsWithTypeWidening` | Allows new columns and supported type widening where available. |

Example with schema evolution:

```python
(
    spark.readStream
        .format("cloudFiles")
        .option("cloudFiles.format", "json")
        .option("cloudFiles.schemaLocation", "/Volumes/de_academy/raw/_schemas/support_tickets")
        .option("cloudFiles.schemaEvolutionMode", "addNewColumns")
        .option("rescuedDataColumn", "_rescued_data")
        .load("/Volumes/de_academy/raw/support_tickets")
        .writeStream
        .option("checkpointLocation", "/Volumes/de_academy/raw/_checkpoints/support_tickets")
        .trigger(availableNow=True)
        .toTable("de_academy.bronze.support_tickets_raw")
)
```

Exam focus:

- New columns in default Auto Loader schema evolution can cause the stream to fail once, update schema metadata, and then succeed after restart.
- Lakeflow Jobs can restart ingestion after schema evolution failures.
- Schema hints are different from fully specifying a schema.
- The rescue column is useful when you do not want unexpected fields to be lost.

## Lakeflow Connect

Lakeflow Connect provides ingestion connectors for local files, enterprise applications, databases, cloud storage, message buses, and more. It uses Databricks platform services such as Unity Catalog governance, Lakeflow Jobs orchestration, and pipeline monitoring.

### Managed Connectors

Managed connectors are the most automated option for supported enterprise applications and databases. They can include source-specific authentication, CDC, retries, schema evolution, edge case handling, and long-term connector maintenance.

Best fit:

- Supported SaaS applications such as Salesforce-style enterprise sources.
- Supported databases such as SQL Server or PostgreSQL connectors.
- Teams that want minimal code and lower operational burden.
- CDC or incremental database replication where the connector supports it.
- Ingestion into Unity Catalog-governed Delta tables.

Typical requirements:

- Unity Catalog-enabled workspace.
- Serverless compute enabled where required.
- Proper privileges such as `CREATE CONNECTION`, `USE CONNECTION`, `USE CATALOG`, `USE SCHEMA`, and `CREATE TABLE`.
- Source-specific setup such as network access and CDC/change tracking where required.

### Standard Connectors

Standard connectors provide more customization than managed connectors. They are used in Lakeflow Spark Declarative Pipelines or Structured Streaming-style patterns.

Best fit:

- Cloud object storage through Auto Loader.
- Message buses such as Kafka, Kinesis, Pub/Sub, or Pulsar.
- SFTP or source patterns where you need custom logic.
- Cases where managed connectors do not support the source or the required behavior.

### Community and Partner Connectors

Community connectors and partner tools can fill gaps when Databricks-managed connectors are not available or when the organization already standardizes on a tool.

Best fit:

- Existing enterprise ingestion tools.
- SaaS sources not supported by managed connectors.
- Reverse ETL or vendor-specific ingestion patterns.
- Cases where a validated partner integration reduces engineering effort.

Exam focus:

- Start with the most managed option that satisfies the requirement.
- Drop down to standard connectors, partner connectors, or custom code only when you need unsupported sources or deeper customization.
- Lakehouse Federation is not ingestion; it queries data in place without copying it.
- Delta Sharing is data sharing, not a general ingestion pattern.

## JDBC, ODBC, and REST Clients in Notebooks

Notebook-based ingestion is useful when a source does not fit a managed connector or when the pipeline requires custom API logic.

### JDBC Read Example

```python
jdbc_url = "jdbc:postgresql://db.example.com:5432/sales"
connection_properties = {
    "user": dbutils.secrets.get("sales_db", "username"),
    "password": dbutils.secrets.get("sales_db", "password"),
    "driver": "org.postgresql.Driver",
}

orders_df = (
    spark.read
        .jdbc(
            url=jdbc_url,
            table="public.orders",
            properties=connection_properties,
        )
)

(
    orders_df.write
        .format("delta")
        .mode("append")
        .saveAsTable("de_academy.bronze.orders_from_jdbc")
)
```

### JDBC Incremental Query Example

```python
last_id = spark.sql("""
  SELECT coalesce(max(order_id), 0) AS max_id
  FROM de_academy.bronze.orders_from_jdbc
""").first()["max_id"]

query = f"(SELECT * FROM public.orders WHERE order_id > {last_id}) AS incremental_orders"

orders_df = spark.read.jdbc(
    url=jdbc_url,
    table=query,
    properties=connection_properties,
)

orders_df.write.mode("append").saveAsTable("de_academy.bronze.orders_from_jdbc")
```

### REST Client Example

```python
import requests

token = dbutils.secrets.get("support_api", "token")
response = requests.get(
    "<support-api-url>/api/v1/tickets",
    headers={"Authorization": f"Bearer {token}"},
    params={"status": "updated"},
    timeout=60,
)
response.raise_for_status()

tickets = response.json()["tickets"]
tickets_df = spark.createDataFrame(tickets)

tickets_df.write.mode("append").saveAsTable("de_academy.bronze.support_tickets")
```

### Landing REST Data to Cloud Storage First

Sometimes you should land raw API responses as files first, then load them with Auto Loader or `COPY INTO`. This preserves a raw source copy and makes reprocessing easier.

```python
import json
from datetime import datetime

payload = response.json()
run_ts = datetime.utcnow().strftime("%Y%m%dT%H%M%SZ")
raw_path = f"/Volumes/de_academy/raw/support_api/tickets_{run_ts}.json"

dbutils.fs.put(raw_path, json.dumps(payload), overwrite=False)
```

Then ingest:

```sql
COPY INTO de_academy.bronze.support_tickets_raw
FROM '/Volumes/de_academy/raw/support_api/'
FILEFORMAT = JSON;
```

Exam focus:

- Use secrets or Unity Catalog connections; do not hard-code credentials.
- Use Lakeflow Jobs to schedule and retry notebook ingestion.
- Use watermarks or source metadata for incremental loads.
- Be careful with JDBC partitioning; too many parallel reads can overload source systems.
- Managed connectors are preferred when they meet the source and operational requirements.

## Semi-Structured and Unstructured Data

Semi-structured data has flexible structure, such as JSON, XML, nested arrays, maps, or event payloads. Unstructured data includes PDFs, images, audio, and text documents.

### JSON as Structs

Use an explicit schema when the fields are known and stable.

```python
from pyspark.sql.types import StructType, StructField, StringType, TimestampType

schema = StructType([
    StructField("event_id", StringType()),
    StructField("user_id", StringType()),
    StructField("event_ts", TimestampType()),
    StructField("event_type", StringType()),
])

events_df = (
    spark.read
        .schema(schema)
        .json("/Volumes/de_academy/raw/events/sample.json")
)
```

### Nested JSON with Auto Loader

```python
(
    spark.readStream
        .format("cloudFiles")
        .option("cloudFiles.format", "json")
        .option("cloudFiles.inferColumnTypes", "true")
        .option("cloudFiles.schemaLocation", "/Volumes/de_academy/raw/_schemas/nested_events")
        .load("/Volumes/de_academy/raw/nested_events")
        .writeStream
        .option("checkpointLocation", "/Volumes/de_academy/raw/_checkpoints/nested_events")
        .trigger(availableNow=True)
        .toTable("de_academy.bronze.nested_events_raw")
)
```

Query nested fields:

```sql
SELECT
  event_id,
  payload.device.os AS device_os,
  payload.geo.country AS country
FROM de_academy.bronze.nested_events_raw;
```

### VARIANT for Flexible Semi-Structured Data

For highly variable JSON, Databricks supports the `VARIANT` type in supported runtimes. This can preserve flexible semi-structured payloads.

```sql
CREATE OR REPLACE TABLE de_academy.bronze.api_payloads (
  source STRING,
  ingestion_ts TIMESTAMP,
  raw VARIANT
);

INSERT INTO de_academy.bronze.api_payloads
SELECT
  'support_api',
  current_timestamp(),
  parse_json('{"ticket_id":"t1","priority":"high","tags":["vip","late"]}');

SELECT
  raw:ticket_id AS ticket_id,
  raw:priority AS priority
FROM de_academy.bronze.api_payloads;
```

Exam focus:

- Use explicit schemas for stable semi-structured data.
- Use rescue columns or flexible types when schemas drift.
- Use volumes for non-tabular files that need governance.
- Use Delta tables for downstream queryable datasets.

## Method Selection Cheat Sheet

| Requirement | Best starting choice | Why |
| --- | --- | --- |
| One-time small CSV upload | Add Data UI or file upload | Simple manual workflow |
| Scheduled SQL file load from cloud storage | `COPY INTO` | Idempotent SQL file ingestion |
| Many files, frequent arrivals, schema drift | Auto Loader | Scalable incremental file discovery and schema handling |
| Supported SaaS/database source with low-code ingestion | Lakeflow Connect managed connector | Source-specific managed ingestion and reduced operations |
| Kafka/Kinesis/Pub/Sub or custom streaming source | Lakeflow Connect standard connector or Structured Streaming | More customization for event streams |
| Existing enterprise ingestion vendor | Partner connector | Reuse validated third-party integration |
| Unsupported REST API with custom pagination | Notebook REST client scheduled by Lakeflow Jobs | Custom logic with orchestration |
| External database with custom SQL extraction | JDBC notebook or UC JDBC connection pattern | Flexible database reads/writes when managed ingestion does not fit |
| Query external source without copying | Lakehouse Federation | This is not ingestion |

## Common Exam Traps

- Choosing local file upload for recurring production ingestion.
- Choosing `COPY INTO` for millions or billions of rapidly arriving files when Auto Loader is a better fit.
- Forgetting that Auto Loader needs checkpoint and schema locations.
- Putting checkpoints inside the source landing directory.
- Treating Lakehouse Federation as ingestion. Federation queries in place; it does not copy data into Delta tables.
- Hard-coding source passwords in notebooks instead of using secrets or governed connections.
- Ignoring Unity Catalog privileges for target catalogs, schemas, tables, volumes, or connections.
- Assuming `COPY INTO` reloads already processed files by default. It skips already loaded files.
- Confusing managed connectors with standard connectors: managed has more source-specific automation; standard gives more customization.
- Ignoring schema drift in JSON/API/event data.

## Mini Labs

### Lab 1: Load Files with `COPY INTO`

```sql
CREATE TABLE IF NOT EXISTS de_academy.bronze.sales_csv (
  sale_id STRING,
  customer_id STRING,
  sale_date DATE,
  amount DOUBLE
)
USING DELTA;

COPY INTO de_academy.bronze.sales_csv
FROM '/Volumes/de_academy/raw/sales_csv/'
FILEFORMAT = CSV
FORMAT_OPTIONS (
  'header' = 'true',
  'inferSchema' = 'false'
);
```

Learning outcome:

- You used SQL to incrementally load files into a governed Delta table.

### Lab 2: Process Files with Auto Loader

```python
(
    spark.readStream
        .format("cloudFiles")
        .option("cloudFiles.format", "json")
        .option("cloudFiles.schemaLocation", "/Volumes/de_academy/raw/_schemas/events")
        .load("/Volumes/de_academy/raw/events")
        .writeStream
        .option("checkpointLocation", "/Volumes/de_academy/raw/_checkpoints/events")
        .trigger(availableNow=True)
        .toTable("de_academy.bronze.events_raw")
)
```

Learning outcome:

- You used Auto Loader for incremental file discovery with checkpointing and schema tracking.

### Lab 3: Choose an Ingestion Tool

For each scenario, choose the likely answer:

1. Daily cloud files and SQL-only team: `COPY INTO`.
2. Millions of files arriving continuously with schema drift: Auto Loader.
3. Supported SaaS source with CDC and minimal code: Lakeflow Connect managed connector.
4. Unsupported API with custom pagination: notebook REST client scheduled by Lakeflow Jobs.
5. Query external source without copying data: Lakehouse Federation, not ingestion.

## Official References

- Databricks Certified Data Engineer Associate Exam Guide, May 4, 2026: https://www.databricks.com/sites/default/files/2026-03/databricks-certified-data-engineer-associate-exam-guide-may-4-2026.pdf
- What is Lakeflow Connect?: https://docs.databricks.com/aws/en/ingestion/overview
- Managed connectors in Lakeflow Connect: https://docs.databricks.com/aws/en/ingestion/lakeflow-connect
- Standard connectors in Lakeflow Connect: https://docs.databricks.com/aws/en/ingestion/
- Get started using COPY INTO: https://docs.databricks.com/aws/en/ingestion/cloud-object-storage/copy-into
- COPY INTO SQL reference: https://docs.databricks.com/aws/en/sql/language-manual/delta-copy-into
- What is Auto Loader?: https://docs.databricks.com/aws/en/ingestion/cloud-object-storage/auto-loader/
- Auto Loader schema inference and evolution: https://docs.databricks.com/aws/en/ingestion/cloud-object-storage/auto-loader/schema
- Auto Loader with Unity Catalog: https://docs.databricks.com/aws/en/ingestion/cloud-object-storage/auto-loader/unity-catalog
- Connect to data sources and external services: https://docs.databricks.com/aws/en/connect/
- Create or modify a table using file upload: https://docs.databricks.com/aws/en/ingestion/create-or-modify-table
- Work with files in Unity Catalog volumes: https://docs.databricks.com/aws/en/volumes/volume-files
- Ingest data as semi-structured VARIANT type: https://docs.databricks.com/aws/en/ingestion/variant
- Lakeflow Jobs schedules and triggers: https://docs.databricks.com/aws/en/jobs/triggers
