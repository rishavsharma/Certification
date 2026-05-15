# Section 1: Databricks Intelligence Platform - Practice Exam

Exam guide version: NEW EXAM GUIDE, valid on or after 4-May-2026.

Question style: multiple-choice with four plausible options, modeled after the sample questions in the attached guide. These are original practice questions, not official exam questions.

## Questions

**Question 1**  
**Objective:** Understand the core components of the Databricks Data Intelligence Platform  
A data engineering team wants a single platform for SQL analytics, PySpark ETL, governed data access, and machine learning workflows over data stored in cloud object storage. Which statement best describes the value of the Databricks Data Intelligence Platform in this scenario?

A. It replaces all cloud object storage with workspace-local file storage.  
B. It combines compute, storage governance, Delta Lake reliability, and collaborative development around lakehouse data.  
C. It requires all workloads to run as SQL dashboards only.  
D. It stores all customer data permanently in the Databricks control plane.

**Question 2**  
**Objective:** Understand the core components of the Databricks Data Intelligence Platform  
A platform administrator is explaining Databricks architecture to a new engineer. The engineer asks where notebook commands, Spark jobs, and SQL queries actually process data. Which layer should the administrator identify?

A. Control plane  
B. Compute plane  
C. Account console only  
D. Unity Catalog privilege model

**Question 3**  
**Objective:** Understand the core components of the Databricks Data Intelligence Platform  
A company uses classic Databricks compute on AWS. The security team asks where the cluster VMs that run Spark workloads are provisioned. Which answer is most accurate for classic compute?

A. In the customer's cloud account, as part of the compute plane  
B. Only in the Databricks account console  
C. In the Delta transaction log  
D. In the Unity Catalog metastore

**Question 4**  
**Objective:** Understand the core components of the Databricks Data Intelligence Platform  
A data engineer references a table as `finance.silver.transactions`. What does the three-level name represent in Unity Catalog?

A. `workspace.notebook.table`  
B. `metastore.cluster.file`  
C. `catalog.schema.object`  
D. `cloud.region.bucket`

**Question 5**  
**Objective:** Understand the core components of the Databricks Data Intelligence Platform  
A team needs ACID transactions, time travel, schema enforcement, and reliable concurrent reads and writes on tables stored in cloud object storage. Which platform component provides these table-level capabilities?

A. Delta Lake  
B. SQL warehouse endpoint permissions  
C. Databricks Git folders  
D. Account-level billing reports

**Question 6**  
**Objective:** Understand architecture, Delta Lake, and Unity Catalog  
A data engineer is asked to explain the difference between Delta Lake and Unity Catalog. Which explanation is correct?

A. Delta Lake governs users and groups, while Unity Catalog stores Parquet files.  
B. Delta Lake provides table reliability and transaction features, while Unity Catalog provides centralized governance and discovery.  
C. Delta Lake is used only for dashboards, while Unity Catalog is used only for Spark clusters.  
D. Delta Lake and Unity Catalog are two names for the same feature.

**Question 7**  
**Objective:** Understand architecture, Delta Lake, and Unity Catalog  
A data engineer creates a table using `CREATE TABLE sales.orders (...) USING DELTA`. What is created?

A. A Delta table whose metadata can be governed by Unity Catalog if the table is in a UC catalog  
B. A SQL warehouse  
C. A Databricks workspace  
D. A serverless jobs compute resource

**Question 8**  
**Objective:** Understand architecture, Delta Lake, and Unity Catalog  
A data engineer needs to inspect previous operations on a Delta table to determine when rows were overwritten. Which SQL command is most appropriate?

A. `DESCRIBE HISTORY catalog.schema.table_name`  
B. `SHOW WORKSPACES catalog.schema.table_name`  
C. `LIST CLUSTERS catalog.schema.table_name`  
D. `REVOKE HISTORY ON catalog.schema.table_name`

**Question 9**  
**Objective:** Understand architecture, Delta Lake, and Unity Catalog  
A pipeline accidentally loaded incorrect data into a Delta table. The team wants to query the table as it existed at an earlier committed version for validation. Which syntax pattern is appropriate?

A. `SELECT * FROM table_name VERSION AS OF 12`  
B. `SELECT * FROM table_name CONTROL PLANE AS OF 12`  
C. `SELECT * FROM table_name WAREHOUSE AS OF 12`  
D. `SELECT * FROM table_name ACCOUNT AS OF 12`

**Question 10**  
**Objective:** Understand architecture, Delta Lake, and Unity Catalog  
A team asks which feature prevents a writer from accidentally inserting a string into a column that is defined as an integer, unless the table is explicitly changed. Which Delta capability is being described?

A. Schema enforcement  
B. Git branching  
C. Warehouse auto-stop  
D. Job retry policy

**Question 11**  
**Objective:** Understand architecture, Delta Lake, and Unity Catalog  
A data engineer wants a governed way to organize data assets so teams can discover tables, views, functions, and volumes by business domain. Which Unity Catalog object is the top-level namespace under a metastore for this purpose?

A. Catalog  
B. Notebook  
C. Cluster node  
D. Spark stage

**Question 12**  
**Objective:** Understand architecture, Delta Lake, and Unity Catalog  
An analyst has been granted `SELECT` on a table but still cannot query it. The table is in `sales_prod.gold.orders`. Which additional privileges are commonly required in the Unity Catalog hierarchy?

A. `USE CATALOG` on `sales_prod` and `USE SCHEMA` on `sales_prod.gold`  
B. `CREATE CLUSTER` on the table and `MODIFY` on the workspace  
C. `READ FILES` on the control plane and `MANAGE JOBS` on the account  
D. `ATTACH NOTEBOOK` on the Delta transaction log

**Question 13**  
**Objective:** Understand compute services and select suitable compute  
A business analyst connects a BI tool to Databricks to run interactive SQL queries against curated gold tables. Which compute service is the best fit?

A. SQL warehouse  
B. Classic all-purpose cluster with R installed  
C. Git folder  
D. Unity Catalog metastore

**Question 14**  
**Objective:** Understand compute services and select suitable compute  
A data engineer is scheduling a daily PySpark notebook ETL job that uses standard DataFrame APIs and does not require custom cluster settings. The team wants minimal compute administration. Which compute option is generally most suitable?

A. Serverless jobs compute  
B. Long-running all-purpose classic compute  
C. Account console  
D. Manually edited files in the Delta table storage path

**Question 15**  
**Objective:** Understand compute services, limitations, and workload selection  
A notebook uses Spark RDD APIs and must continue using them. Which compute choice is most appropriate?

A. Serverless notebook compute, because it supports all Spark APIs  
B. Classic compute, because serverless notebooks and jobs do not support RDD APIs  
C. SQL warehouse, because RDDs are SQL objects  
D. Unity Catalog metastore, because it executes RDD code

**Question 16**  
**Objective:** Understand compute services, limitations, and workload selection  
A team has an R notebook that performs exploratory statistics on governed tables. Which compute option should they avoid if they need R support?

A. Classic all-purpose compute  
B. Serverless notebooks/jobs compute  
C. Classic jobs compute configured for the workload  
D. A policy-approved classic cluster

**Question 17**  
**Objective:** Understand compute services and select suitable compute  
A production Spark job requires an init script, custom Spark configuration, and a library installed at cluster startup. Serverless compute does not support the required customization. Which compute should the engineer choose?

A. Classic jobs compute  
B. Serverless SQL warehouse  
C. Workspace Repos  
D. Delta Sharing recipient

**Question 18**  
**Objective:** Understand compute services and select suitable compute  
A team leaves an all-purpose cluster running overnight after interactive development. Which cost-related risk does this create?

A. Idle compute can continue to accrue cost even when no useful work is running.  
B. Unity Catalog automatically deletes all tables.  
C. Delta Lake disables ACID transactions.  
D. SQL warehouses cannot run queries the next day.

**Question 19**  
**Objective:** Understand compute services and cost models  
Which statement best describes Databricks Units (DBUs)?

A. A normalized unit used to measure Databricks compute usage for billing.  
B. A replacement for Unity Catalog privileges.  
C. A file format that replaces Parquet.  
D. A type of notebook command cell.

**Question 20**  
**Objective:** Understand compute services and cost models  
A company is comparing classic compute and serverless compute. Which cost/operations statement is most accurate at an exam level?

A. Classic compute often exposes both Databricks usage and cloud infrastructure management, while serverless shifts more infrastructure management to Databricks.  
B. Serverless compute always costs zero DBUs.  
C. Classic compute cannot be used for production jobs.  
D. Serverless compute requires the user to manually provision every VM.

**Question 21**  
**Objective:** Understand compute services and select suitable compute  
A data engineer is building a SQL-based dashboard that refreshes frequently for executives. The team wants fast startup and minimal infrastructure management. Which option should they select?

A. Serverless SQL warehouse  
B. Classic all-purpose cluster kept running 24/7  
C. Unity Catalog volume  
D. Local CSV files uploaded to the workspace

**Question 22**  
**Objective:** Understand compute services and select suitable compute  
A data scientist needs long-lived interactive development and uses custom cluster libraries not supported by serverless. Which compute service is the best fit?

A. Classic all-purpose compute  
B. Serverless SQL warehouse  
C. Delta table history  
D. Account billing export

**Question 23**  
**Objective:** Understand compute services and select suitable compute  
A data engineering job runs a packaged JAR with Spark submit and requires custom JVM settings. Which compute choice is most appropriate?

A. Classic jobs compute  
B. Serverless SQL warehouse  
C. Unity Catalog catalog  
D. Lakehouse Monitoring dashboard

**Question 24**  
**Objective:** Understand compute services and select suitable compute  
A data engineer creates a Lakeflow Spark Declarative Pipeline without special customization requirements. Which compute option is usually recommended where available?

A. Serverless pipeline compute  
B. A manually maintained all-purpose cluster  
C. A local laptop running Spark only  
D. A Unity Catalog metastore

**Question 25**  
**Objective:** Understand compute services, characteristics, and limitations  
A team wants to troubleshoot a serverless notebook job using a traditional Spark UI. What should they know?

A. Spark UI is not available for serverless notebooks/jobs; use supported serverless query profiling or monitoring tools instead.  
B. Spark UI is the only way to access Unity Catalog.  
C. Spark UI creates the Delta transaction log.  
D. Spark UI is required to create SQL warehouses.

**Question 26**  
**Objective:** Understand compute services and select suitable compute  
A data engineer must choose compute for a notebook task in Lakeflow Jobs. The task uses supported PySpark DataFrame APIs and has no custom cluster dependencies. Which option aligns best with hands-off operation?

A. Serverless jobs compute  
B. Classic all-purpose compute permanently running  
C. SQL warehouse, because all notebooks must run on SQL warehouses  
D. External location, because it runs notebooks

**Question 27**  
**Objective:** Understand compute services and select suitable compute  
A data engineer is configuring compute for a SQL task in a job. The task executes a parameterized SQL query against Unity Catalog tables. Which compute is the most natural fit?

A. SQL warehouse  
B. Classic all-purpose cluster with R  
C. Databricks Git branch  
D. Delta table checkpoint

**Question 28**  
**Objective:** Understand compute services, characteristics, and limitations  
A team wants a compute option with fast startup, automatic scaling, Photon enabled by default, and minimal infrastructure tuning. Which option best matches these characteristics?

A. Serverless compute  
B. Manually created single-node classic cluster with auto-termination disabled  
C. Unity Catalog schema  
D. Delta transaction log

**Question 29**  
**Objective:** Understand compute services, characteristics, and limitations  
Which statement about Photon is correct?

A. Photon accelerates supported SQL and DataFrame workloads without requiring application code changes.  
B. Photon is a separate table governance system that replaces Unity Catalog.  
C. Photon requires all code to be rewritten in Scala.  
D. Photon stores all data in the control plane.

**Question 30**  
**Objective:** Understand compute services, characteristics, and limitations  
A PySpark workload is dominated by unsupported UDF-heavy logic and RDD operations. Which expectation about Photon and serverless is most realistic?

A. The workload may not benefit from Photon and may require classic compute if it depends on unsupported RDD behavior.  
B. Photon guarantees the workload will run 100 times faster.  
C. Unity Catalog will automatically rewrite the UDFs into SQL.  
D. SQL warehouses are required for all PySpark workloads.

**Question 31**  
**Objective:** Understand architecture, Delta Lake, and Unity Catalog  
A data engineer wants to grant governed access to files that are not tables, such as landing-zone JSON files used by a pipeline. Which Unity Catalog object is most relevant for file access?

A. Volume  
B. Notebook revision  
C. Spark executor  
D. SQL warehouse size

**Question 32**  
**Objective:** Understand architecture, Delta Lake, and Unity Catalog  
A table is registered in Unity Catalog but stored at a cloud storage path managed outside Databricks. Which table type does this describe at a high level?

A. External table  
B. Account console table  
C. Serverless table  
D. Notebook table

**Question 33**  
**Objective:** Understand architecture, Delta Lake, and Unity Catalog  
A table is created in Unity Catalog and Databricks manages the table lifecycle and storage location. Which table type does this describe?

A. Managed table  
B. SQL warehouse  
C. Compute plane  
D. Delta Sharing recipient

**Question 34**  
**Objective:** Understand the core components of the Databricks Data Intelligence Platform  
A company attaches the same Unity Catalog metastore to multiple workspaces in the same supported region context. What is the primary benefit?

A. Users can access a consistent governed namespace for data assets across those workspaces.  
B. All workspaces must use the same notebook file for every job.  
C. All data is copied into the control plane.  
D. SQL warehouses are automatically deleted.

**Question 35**  
**Objective:** Understand the core components of the Databricks Data Intelligence Platform  
A junior engineer says, "The Databricks control plane is where Spark executors scan and join the customer data." What is the best correction?

A. Spark execution runs in the compute plane; the control plane manages platform services and orchestration metadata.  
B. The control plane and Delta transaction log are identical.  
C. Spark executors run inside the account console only.  
D. Unity Catalog privileges are the same thing as physical cluster nodes.

**Question 36**  
**Objective:** Understand compute services and cost models  
A team wants to reduce waste from classic interactive clusters. Which setting or practice is most directly relevant?

A. Configure auto-termination and right-size the cluster.  
B. Disable Delta Lake transactions.  
C. Store table data in notebook source files.  
D. Remove Unity Catalog catalogs.

**Question 37**  
**Objective:** Understand compute services and select suitable compute  
A data engineer needs to run BI queries from Tableau against governed Delta tables. Which compute endpoint should the BI tool usually connect to?

A. SQL warehouse  
B. Notebook cell output  
C. Delta transaction log JSON file  
D. Cluster driver local disk

**Question 38**  
**Objective:** Understand compute services, characteristics, and limitations  
A workload requires GPU-enabled instances for a specialized processing task. Serverless does not meet the requirement. Which answer is most appropriate?

A. Use classic compute configured with the required GPU instance type where supported.  
B. Use Unity Catalog privileges to create GPUs.  
C. Use Delta time travel to add GPUs.  
D. Use a SQL dashboard, because dashboards provide GPUs automatically.

**Question 39**  
**Objective:** Understand compute services and select suitable compute  
A team has a one-time exploratory notebook that uses only supported Python and SQL operations. They want quick startup and no cluster configuration. Which option is best where available?

A. Serverless notebook compute  
B. A manually tuned production classic jobs cluster  
C. A SQL warehouse only, because notebooks cannot use Python  
D. A Unity Catalog schema

**Question 40**  
**Objective:** Understand architecture, Delta Lake, and Unity Catalog  
A data engineer directly deletes Parquet files from a Delta table directory using a cloud storage console. Why is this a bad practice?

A. It bypasses Delta transaction log semantics and can corrupt or invalidate table state.  
B. It creates a new Unity Catalog metastore automatically.  
C. It reduces DBU costs to zero.  
D. It is the recommended way to compact Delta files.

**Question 41**  
**Objective:** Understand architecture, Delta Lake, and Unity Catalog  
A team needs to compact many small files in a Delta table to improve scan performance. Which SQL command is appropriate?

A. `OPTIMIZE catalog.schema.table_name`  
B. `CREATE WORKSPACE catalog.schema.table_name`  
C. `DROP CONTROL PLANE catalog.schema.table_name`  
D. `USE WAREHOUSE FILES catalog.schema.table_name`

**Question 42**  
**Objective:** Understand architecture, Delta Lake, and Unity Catalog  
A team wants Databricks to manage clustering/layout decisions more flexibly than static partitioning for supported Delta tables. Which feature should they recognize?

A. Liquid clustering  
B. Notebook comments  
C. Account password policy  
D. Workspace export

**Question 43**  
**Objective:** Understand compute services and cost models  
A team asks why an all-purpose cluster is usually not the best production job compute. Which answer is most accurate?

A. All-purpose clusters are designed for interactive development and can remain running idle; jobs compute is better aligned to automated execution.  
B. All-purpose clusters cannot read Delta tables.  
C. All-purpose clusters bypass Unity Catalog by design.  
D. All-purpose clusters are the only way to run scheduled jobs.

**Question 44**  
**Objective:** Understand compute services and select suitable compute  
A SQL analyst says they need a compute service optimized for BI workloads, JDBC/ODBC connectivity, and SQL query execution. Which option should they use?

A. SQL warehouse  
B. Classic jobs compute running a Python wheel  
C. Unity Catalog external location  
D. Delta Sharing share

**Question 45**  
**Objective:** Understand architecture, Delta Lake, and Unity Catalog  
A team wants an audit-friendly, governed way to discover who can access tables across catalogs and schemas. Which platform layer is primarily involved?

A. Unity Catalog  
B. Delta file compaction only  
C. Notebook cell formatting  
D. Photon vectorization only

**Question 46**  
**Objective:** Understand compute services, characteristics, and limitations  
A streaming-style pipeline is being migrated to serverless. The engineer sees guidance to use `AvailableNow` style processing rather than unsupported continuous-style triggers. What does this indicate?

A. Some streaming trigger patterns are limited on serverless, so the pipeline should use supported trigger modes.  
B. Serverless cannot read any Delta tables.  
C. SQL warehouses are required for all streaming workloads.  
D. Unity Catalog disables all streaming.

**Question 47**  
**Objective:** Understand compute services and cost models  
A company wants faster startup for repeated classic jobs without leaving clusters running all day. Which classic-compute feature can help reduce startup latency?

A. Instance pools  
B. Delta time travel  
C. Unity Catalog schemas  
D. Notebook markdown cells

**Question 48**  
**Objective:** Understand compute services and select suitable compute  
A data engineer is choosing between serverless and classic compute. The workload requires a Spark configuration that serverless does not allow. What is the best decision?

A. Use classic compute where the needed Spark configuration can be applied.  
B. Use serverless anyway because all Spark configurations are supported.  
C. Use Unity Catalog to run the Spark configuration.  
D. Use Delta Sharing to execute the job.

**Question 49**  
**Objective:** Understand architecture, Delta Lake, and Unity Catalog  
A data engineer needs to create a table that can support both batch upserts and streaming reads/writes with reliable transactions. Which table technology is the best Databricks-native choice?

A. Delta Lake table  
B. Workspace notebook file  
C. Plain text file without metadata  
D. Account billing CSV

**Question 50**  
**Objective:** Understand compute services and select suitable compute  
A team has three workloads: analyst dashboards, scheduled standard PySpark ETL, and an interactive notebook using R. Which mapping is most appropriate?

A. Dashboards on SQL warehouse; standard scheduled PySpark ETL on serverless jobs compute; R notebook on classic all-purpose compute.  
B. Dashboards on Unity Catalog; PySpark ETL on Delta transaction log; R notebook on SQL warehouse.  
C. Dashboards on classic jobs compute only; PySpark ETL on account console; R notebook on serverless only.  
D. All workloads must run on the same all-purpose cluster to be governed.

## Answer Key

**Question 1: B**  
B is correct because the platform unifies lakehouse storage, Delta reliability, governance, compute, and collaborative development. A is wrong because Databricks commonly works with cloud object storage rather than replacing it with workspace-local storage. C is wrong because Databricks supports SQL, PySpark, jobs, notebooks, pipelines, and more. D is wrong because customer data processing does not generally mean permanent storage in the control plane.

**Question 2: B**  
B is correct because notebook commands, Spark jobs, and SQL queries process data in compute resources in the compute plane. A is wrong because the control plane manages platform services and metadata, not the actual Spark execution. C is only the administrative UI. D is a governance model, not an execution layer.

**Question 3: A**  
A is correct for classic compute: the compute plane resources are provisioned in the customer's cloud account or subscription. B is wrong because the account console is administrative. C is wrong because the Delta transaction log tracks table changes, not VMs. D is wrong because Unity Catalog stores governance metadata, not clusters.

**Question 4: C**  
C is correct: Unity Catalog uses `catalog.schema.object`. A confuses workspace assets with data namespace. B invents a metastore-cluster-file hierarchy. D describes cloud infrastructure, not Unity Catalog naming.

**Question 5: A**  
A is correct because Delta Lake provides ACID transactions, schema enforcement, time travel, and reliable table operations on object storage. B is a compute/access endpoint concept. C is a code collaboration feature. D is billing metadata, not table reliability.

**Question 6: B**  
B is correct: Delta Lake handles the transaction/storage layer, while Unity Catalog handles governance, discovery, access control, and lineage. A reverses the responsibilities. C incorrectly limits both features. D is wrong because they are different platform components that often work together.

**Question 7: A**  
A is correct because `USING DELTA` creates a Delta table, and if the name is in a Unity Catalog catalog, UC can govern it. B, C, and D are compute or workspace resources, not table objects.

**Question 8: A**  
A is correct because `DESCRIBE HISTORY` shows committed Delta table operations. B and C are not valid table history commands. D is not how history is inspected or governed.

**Question 9: A**  
A is correct because Delta time travel supports `VERSION AS OF` queries. B, C, and D use unrelated platform terms and are not valid time travel syntax.

**Question 10: A**  
A is correct because schema enforcement prevents incompatible writes unless schema changes are allowed. B is source control. C is compute cost management. D controls job retry behavior, not table schema validation.

**Question 11: A**  
A is correct because a catalog is the top-level namespace under a Unity Catalog metastore. B, C, and D are workspace or execution concepts, not UC data organization objects.

**Question 12: A**  
A is correct because users typically need permission to use the containing catalog and schema in addition to object-level privileges. B, C, and D mix unrelated compute, workspace, and storage terms.

**Question 13: A**  
A is correct because SQL warehouses are built for SQL analytics, BI connectivity, dashboards, and interactive SQL. B can run code but is not the best BI endpoint. C is source control. D governs data but does not execute queries.

**Question 14: A**  
A is correct because serverless jobs compute fits supported automated PySpark notebook jobs with minimal administration. B can work but risks idle cost and more management. C and D are not compute services for running the job.

**Question 15: B**  
B is correct because serverless notebooks/jobs do not support Spark RDD APIs, so classic compute is appropriate for workloads that depend on RDDs. A is factually wrong. C is for SQL, not RDD APIs. D governs data, not execution.

**Question 16: B**  
B is correct as the option to avoid because serverless notebooks/jobs do not support R. A, C, and D are classic compute options that can be configured for R use cases.

**Question 17: A**  
A is correct because classic jobs compute supports production jobs that need custom cluster-level configuration not available on serverless. B is SQL compute, not the right Spark customization target. C and D do not run the job.

**Question 18: A**  
A is correct because idle all-purpose compute can continue to incur cost. B, C, and D are unrelated consequences and do not follow from leaving a cluster running.

**Question 19: A**  
A is correct because DBUs are a normalized unit for measuring Databricks compute usage for billing. B, C, and D confuse DBUs with governance, file formats, or notebooks.

**Question 20: A**  
A is correct because classic compute exposes more cloud infrastructure management, while serverless shifts infrastructure operations to Databricks. B is wrong because serverless is not free. C is too broad; classic jobs compute is valid for production. D describes classic provisioning, not serverless.

**Question 21: A**  
A is correct because serverless SQL warehouses fit frequent SQL dashboards with fast startup and low infrastructure management. B can be expensive and less SQL-optimized. C governs files and D is a data source pattern, not a compute endpoint.

**Question 22: A**  
A is correct because classic all-purpose compute fits interactive development requiring custom libraries or settings not supported by serverless. B is SQL-only. C and D are metadata/history concepts, not interactive compute.

**Question 23: A**  
A is correct because classic jobs compute is appropriate for production Spark jobs with JARs, Spark submit, and custom JVM settings. B is SQL compute. C and D are governance/monitoring concepts.

**Question 24: A**  
A is correct because serverless pipeline compute is usually recommended for supported Lakeflow Spark Declarative Pipelines. B adds unnecessary management. C is outside the platform. D governs data and does not execute pipelines.

**Question 25: A**  
A is correct because serverless notebooks/jobs do not expose the traditional Spark UI; use supported serverless profiling and monitoring alternatives. B, C, and D falsely assign unrelated platform functions to Spark UI.

**Question 26: A**  
A is correct because supported PySpark notebook tasks in Lakeflow Jobs are a good fit for serverless jobs compute. B may work but wastes management effort and cost. C is wrong because notebooks can run Python and do not have to run on SQL warehouses. D is a governed storage object, not compute.

**Question 27: A**  
A is correct because SQL tasks should use SQL-optimized compute. B is not the natural fit for a SQL task. C is version control. D is table streaming/storage metadata, not query execution.

**Question 28: A**  
A is correct because serverless compute is designed for fast startup, automatic scaling, Databricks-managed infrastructure, and Photon by default where supported. B is manually managed and idle-prone. C and D are governance/storage concepts.

**Question 29: A**  
A is correct because Photon accelerates supported SQL and DataFrame operations without code rewrites. B confuses Photon with Unity Catalog. C is wrong because Photon does not require Scala rewrites. D is wrong because Photon is an execution engine, not control-plane storage.

**Question 30: A**  
A is correct because RDD and unsupported UDF-heavy workloads may not benefit from Photon and may require classic compute. B is an unrealistic guarantee. C is not a Unity Catalog function. D is wrong because PySpark workloads do not all require SQL warehouses.

**Question 31: A**  
A is correct because Unity Catalog volumes provide governed access to non-tabular files. B, C, and D are unrelated to governed file storage.

**Question 32: A**  
A is correct because an external table is registered in Databricks/Unity Catalog while the underlying storage location is managed outside Databricks. B, C, and D are not valid table categories.

**Question 33: A**  
A is correct because a managed table has its lifecycle and storage location managed by Databricks/Unity Catalog. B is compute, C is architecture, and D is a data sharing identity.

**Question 34: A**  
A is correct because attaching a metastore to multiple workspaces enables a consistent governed namespace. B, C, and D describe effects that do not follow from metastore attachment.

**Question 35: A**  
A is correct because Spark executors process data in the compute plane, while the control plane manages platform services and metadata. B, C, and D confuse unrelated platform layers.

**Question 36: A**  
A is correct because auto-termination and right-sizing directly reduce idle cost and over-provisioning. B would damage table reliability. C and D are not cost-management practices.

**Question 37: A**  
A is correct because BI tools typically connect to SQL warehouses using JDBC/ODBC. B is not a stable BI endpoint. C and D are internal storage/execution details, not query endpoints.

**Question 38: A**  
A is correct because GPU requirements generally call for classic compute configured with suitable instance types where supported. B, C, and D assign GPU provisioning to unrelated features.

**Question 39: A**  
A is correct because serverless notebook compute is a good fit for supported interactive Python/SQL exploration with quick startup. B is overbuilt for one-time exploration. C is wrong because notebooks can use Python. D is a namespace, not compute.

**Question 40: A**  
A is correct because directly deleting table files bypasses Delta transaction log guarantees and can break table consistency. B, C, and D are false consequences.

**Question 41: A**  
A is correct because `OPTIMIZE` compacts small files in supported Delta tables. B, C, and D are not valid Delta maintenance commands.

**Question 42: A**  
A is correct because liquid clustering is designed to manage Delta table clustering more flexibly than static partitioning strategies. B, C, and D are unrelated.

**Question 43: A**  
A is correct because all-purpose clusters are intended for interactive use and can create idle-cost risk; jobs compute is better for automated execution. B and C are false. D is wrong because jobs can run on jobs compute or serverless jobs compute.

**Question 44: A**  
A is correct because SQL warehouses are optimized for SQL, BI, JDBC/ODBC, and dashboard workloads. B is for Python/Spark job workloads. C governs storage access. D shares data, not compute.

**Question 45: A**  
A is correct because Unity Catalog provides centralized governance, permissions, discovery, and audit-friendly metadata. B only addresses physical layout. C and D do not manage access governance.

**Question 46: A**  
A is correct because serverless has limitations around certain streaming trigger patterns, so supported trigger modes should be used. B, C, and D are too broad and false.

**Question 47: A**  
A is correct because instance pools can reduce startup latency for classic compute without keeping every cluster running. B, C, and D do not reduce classic cluster startup time.

**Question 48: A**  
A is correct because unsupported serverless Spark configuration requirements should move the workload to classic compute. B is false. C and D are governance/sharing features, not compute configuration engines.

**Question 49: A**  
A is correct because Delta Lake tables support reliable batch and streaming workloads with transactional guarantees. B, C, and D do not provide Delta table semantics.

**Question 50: A**  
A is correct because SQL dashboards map to SQL warehouses, supported scheduled PySpark ETL maps to serverless jobs compute, and R notebooks require classic all-purpose compute. B, C, and D incorrectly map execution workloads to governance, metadata, or a single unnecessary cluster.

