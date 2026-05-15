# Platform Value and Core Components (Section 1)

## Scope / Objectives (Current Blueprint Wording)

This guide supports the Section 1 objective:

- **Explain the value of the Data Intelligence Platform.**

In this exam version (live through **May 3, 2026**), you are expected to explain *why* Databricks exists (lakehouse + governance + multiple workloads) and to name the platform building blocks that make those benefits real.

## Terminology Drift Note

Common "same idea, different name" pairs you should normalize:

- **Databricks Lakehouse Platform** (older branding) -> **Databricks Data Intelligence Platform** (current docs/exam guide)
- **Data plane** (older) -> **Compute plane** (current docs); and Databricks distinguishes **classic compute plane** vs **serverless compute plane**
- **SQL endpoints** (legacy) -> **SQL warehouses**

If a question uses older terms, map them mentally to current docs without changing the intent.

## Core Concepts

### 1) What Databricks Is (Value Proposition)

Databricks positions itself as a unified platform to build, run, and govern:

- Data engineering pipelines
- SQL analytics and BI
- Data science and AI workloads

The practical value comes from reducing "system sprawl":

- Fewer copies of the same data in different systems
- One governance model (permissions, discovery, lineage) across multiple workloads
- Shared storage formats (not proprietary) that different engines and tools can read

### 2) Lakehouse as the Architectural Pattern

The lakehouse pattern combines:

- Data lake properties: low-cost scalable object storage, open file formats
- Warehouse properties: performance features, SQL usability, ACID tables, governance

On Databricks, the lakehouse is typically implemented with:

- **Delta Lake** for transactional tables and performance features
- **Unity Catalog** for centralized governance
- Multiple compute services (serverless/classic/SQL warehouses) to run different workload styles

### 3) Workspace, Control Plane, and Compute Plane

At a high level:

- **Control plane**: Databricks-managed services and the web application (the workspace UI, job orchestration, metadata services, etc.)
- **Compute plane**: where code runs and data is processed
  - **Classic compute plane**: compute resources run in *your* cloud account (customer-managed networking/infra)
  - **Serverless compute plane**: compute resources run in a Databricks-managed environment (faster startup, less management)

This distinction shows up indirectly in exam questions about:

- Operational overhead (who manages what)
- Startup times and elasticity
- Why serverless options are usually recommended when supported

### 4) Delta Lake (Storage Layer for Tables)

Delta Lake is the table format and storage layer used broadly on Databricks:

- ACID transactions and reliable concurrent reads/writes
- Schema enforcement and evolution support
- Performance primitives like file compaction and clustering-oriented optimizations (`OPTIMIZE`, liquid clustering, etc.)

Even if a question is phrased as "file layout," the mental model is usually "Delta tables are made of Parquet files plus a transaction log."

### 5) Unity Catalog (Governance Layer)

Unity Catalog provides centralized governance across workspaces, including:

- A three-level namespace: `<catalog>.<schema>.<object>`
- Fine-grained access controls (SQL-grant style)
- Discovery and lineage features (conceptually; deeper permissions are Section 5)

In Section 1, Unity Catalog matters mainly because several performance automation features (for example, **predictive optimization**) are specifically described for **Unity Catalog managed tables**.

### 6) Databricks SQL and SQL Warehouses

Databricks SQL provides:

- SQL editor and developer workflows
- Dashboards and BI patterns
- SQL execution on **SQL warehouses** (SQL-optimized compute)

SQL warehouses are the compute choice when the task is primarily SQL analytics/BI, and they also show up in "which compute should I use?" scenarios.

### 7) Photon (Performance Engine)

Photon is Databricks' vectorized query engine that accelerates:

- SQL workloads
- DataFrame operations that compile down to supported operators

You do not need a deep operator-level understanding for Section 1, but you should know Photon is a major part of "performance at scale" value.

## Examples / Decision Tables

### A) Component-to-Outcome Mapping (Exam-Style)

| Business/technical need | Databricks component you should name | Why it matters |
|---|---|---|
| "One table format for batch + streaming + BI + DS" | Delta Lake | ACID tables + performant reads/writes on open storage |
| "Unified permissions and discovery across workspaces" | Unity Catalog | Central governance, consistent object naming, lineage hooks |
| "Fast SQL dashboards and interactive analytics" | Databricks SQL + SQL warehouses | SQL-optimized compute and user-facing SQL tools |
| "Reduce operational overhead managing infra" | Serverless compute | Databricks-managed scaling, fast start, fewer knobs |
| "Run Spark engineering code with customization" | Classic compute | Customer-managed compute with advanced configuration options |

### B) Architecture Sketch (Words You Can Use in Answers)

If asked to explain the platform architecture, a safe high-level answer is:

1. Users work in a Databricks **workspace** (control plane UI and services).
2. Their code runs on **compute** in the compute plane (serverless or classic; SQL warehouses for SQL).
3. Tables live in object storage using open formats (commonly **Delta**) and are governed centrally by **Unity Catalog**.
4. The platform adds performance automation and accelerators (for example Photon, predictive optimization, clustering) to reduce manual tuning.

## Common Exam Traps

- Treating Databricks as "just Spark": Spark is foundational, but the exam focuses on platform-level value (governance, multiple workloads, managed services, SQL warehouses, etc.).
- Mixing up **control plane** (Databricks account) vs **classic compute plane** (your cloud account) vs **serverless compute plane** (Databricks-managed compute environment).
- Overfocusing on governance details: permissions mechanics are heavily tested in Section 5; in Section 1 you mainly need to know *what Unity Catalog is* and that many modern optimizations assume UC managed tables.
- Assuming "SQL warehouse" is just a renamed cluster. Warehouses are SQL-optimized compute with their own sizing and type choices.

## Official Sources (Links)

- What is Databricks? (Data Intelligence Platform overview):  
  https://docs.databricks.com/aws/en/introduction
- High-level architecture (control plane vs compute plane):  
  https://docs.databricks.com/aws/en/getting-started/high-level-architecture
- What is a data lakehouse?:  
  https://docs.databricks.com/aws/en/lakehouse/
- What is Delta Lake in Databricks?:  
  https://docs.databricks.com/aws/en/delta/
- What is Unity Catalog?:  
  https://docs.databricks.com/en/data-governance/unity-catalog/index.html
- Data warehousing on Databricks (Databricks SQL):  
  https://docs.databricks.com/aws/en/sql/
- What is Photon?:  
  https://docs.databricks.com/aws/en/runtime/photon.html
