# Delta Sharing and Lakehouse Federation (Current exam through May 3, 2026)

## Scope and Objectives (current-exam wording)
- Use the Delta Sharing feature available with Unity Catalog to share data.
- Identify the advantages and limitations of Delta sharing.
- Identify types of delta sharing- Databricks vs external system.
- Analyze the cost considerations of data sharing across clouds.
- Identify Use cases of Lakehouse Federation when connected to external sources.

## Terminology Drift Note
- Databricks documentation uses:
  - **Open sharing** to mean sharing to recipients who might not be on Databricks (external systems) using the open Delta Sharing protocol.
  - **Databricks-to-Databricks sharing** to mean sharing to another Unity Catalog metastore (including other Databricks accounts).
- Databricks documentation frequently calls Lakehouse Federation "**query federation**". In exam answers, both terms are usually referring to the same capability: using UC-managed connections to query external databases as **foreign** catalogs/tables.
- Newer governance features (ABAC, row/column filtering, DENY) are not current-exam objectives; treat them as awareness only.

## Core Concepts

## Part 1: Delta Sharing

### 1) Mental model: provider, share, recipient, and shared catalog
- **Provider**: the organization/workspace that shares data.
- **Share**: a container of tables (and optionally views, volumes, notebooks, models depending on protocol) that the provider makes available.
- **Recipient**: the organization/user that receives shared data.
- **Recipient-side shared catalog**: in Databricks-to-Databricks, recipients typically "mount" the share by creating a catalog from it, then use normal UC grants internally.

### 2) Types of Delta Sharing (what "Databricks vs external system" means)
**A) Open sharing (external systems)**
- Recipient does not need to be on Databricks.
- Authentication commonly uses a **credential file / bearer token** or OIDC federation (provider-managed).
- Recipients can use open Delta Sharing connectors (Spark, pandas, BI tools, etc).

**B) Databricks-to-Databricks sharing**
- Recipient is a Databricks user on a UC-enabled workspace attached to a different metastore than the provider.
- No credential file is required for the recipient; Databricks manages the secure connection.
- Supports sharing additional Databricks assets (for example notebooks/volumes/models) that are not available in open sharing.

Exam cue:
- If the recipient is "a partner using Power BI without Databricks", you are in **open sharing**.
- If the recipient is "another Databricks account" or "another UC metastore", you are in **Databricks-to-Databricks**.

### 3) How to share from Unity Catalog (provider-side)
You can manage shares via Catalog Explorer (UI) or SQL.

Common SQL flow:

```sql
-- Provider side: create a share
CREATE SHARE IF NOT EXISTS sales_share;

-- Add a table (requires that you have SELECT on the table and USE SCHEMA on its schema)
ALTER SHARE sales_share ADD TABLE main.sales.transactions;

-- Create a recipient (recipient type depends on protocol)
CREATE RECIPIENT IF NOT EXISTS partner_analytics
COMMENT 'External partner analytics team';

-- Grant access to the share
GRANT SELECT ON SHARE sales_share TO RECIPIENT partner_analytics;
```

What the exam often tests:
- The object you add to is a **share**.
- The object you grant to is a **recipient** (or recipient-side catalog after it's mounted, depending on viewpoint).
- Adding tables to a share is gated by **ownership** and permissions (for example maintaining `SELECT` on the table while it is shared).

### 4) Advantages and limitations (high-signal list)
**Advantages**
- No data replication required (single logical copy).
- Works across clouds/accounts (especially with Databricks-to-Databricks).
- Secure access controlled via sharing constructs plus auditing.

**Limitations (exam-relevant framing)**
- Shared data is **read-only** to recipients.
- Format support and feature support are limited; focus on the big categories:
  - Shareable tabular assets are typically Delta and managed Iceberg table formats.
  - Views have restrictions; not every view/function pattern is shareable.

## Part 2: Cross-cloud / cross-region cost considerations (Delta Sharing)

### 1) Egress is the key cost concept
Delta Sharing avoids replication, but cloud vendors can charge **network egress** when data crosses:
- cloud regions
- cloud providers

Rule of thumb:
- **Within the same region**: you typically incur **no egress**.
- **Across region/cloud**: egress charges may apply and are billed by the cloud vendor.

### 2) Exam-ready reasoning patterns
- "This share is across clouds and accessed frequently":
  - likely egress is non-trivial; consider replication strategies to localize reads (provider-side replicas or recipient-side clones where appropriate).
- "This share is occasional access for a small dataset":
  - Delta Sharing can be cost-effective even with some egress.

## Part 3: Lakehouse Federation (Query Federation)

### 1) What Lakehouse Federation is (in one sentence)
Lakehouse Federation lets you query data in external systems (for example operational databases or cloud warehouses) from Databricks, governed by Unity Catalog, without first ingesting that data into Delta tables.

### 2) The UC objects you must recognize
- **Connection**: stores credentials and connection details to an external database system.
  - Created via Catalog Explorer or `CREATE CONNECTION`.
- **Foreign catalog**: exposes schemas/tables from the external system into Unity Catalog.
  - Created via Catalog Explorer or `CREATE FOREIGN CATALOG`.
- **Foreign tables**: objects inside a foreign catalog that represent external tables.
  - For Lakehouse Federation external sources, foreign tables are typically **read-only** and do not provide the transactional guarantees of UC managed tables.

### 3) Common use cases (what the objective is really asking)
- **Data virtualization for analytics**: run exploratory queries on an external system from Databricks without copying data first.
- **Cross-system joins**: join external tables with lakehouse tables in a single query to validate, enrich, or migrate workloads.
- **Incremental migration**: move workloads from an external warehouse/DB to Databricks by starting with federated access, then ingesting/optimizing later.
- **Central governance**: use UC permissions to manage who can see/query external sources via the foreign catalog.

### 4) Decision table: Delta Sharing vs Lakehouse Federation
| Requirement | Choose | Why |
|---|---|---|
| Give another organization read-only access to curated tables | Delta Sharing | External recipient access, standardized sharing constructs |
| Access an external database live from Databricks for joins and exploration | Lakehouse Federation | Query federation; no need to replicate immediately |
| Avoid cross-cloud egress for frequent heavy reads | Often replication or co-locate workloads | Delta Sharing can trigger egress across boundaries |

## Common Exam Traps
- Thinking Delta Sharing is needed to share data between two workspaces attached to the **same UC metastore**. In that case, normal UC permissions are sufficient.
- Mixing up "share" and "catalog":
  - Provider creates a **share**.
  - Recipient typically creates a **catalog** from a share to expose it to their users with UC permissions.
- Answering "Lakehouse Federation is data sharing":
  - It's query federation (virtualization), not a data sharing protocol like Delta Sharing.
- Assuming you can write to external-source foreign tables:
  - For Lakehouse Federation external sources, foreign tables are generally read-only and don't behave like managed Delta tables.

## Official-Source Links (Databricks)
- Delta Sharing overview (open vs Databricks-to-Databricks, objects, limitations, FAQ): https://docs.databricks.com/aws/en/delta-sharing
- Create and manage shares (provider): https://docs.databricks.com/en/delta-sharing/create-share.html
- ALTER SHARE (SQL syntax + required permissions): https://docs.databricks.com/en/sql/language-manual/sql-ref-syntax-ddl-alter-share.html
- CREATE RECIPIENT (SQL syntax): https://docs.databricks.com/gcp/en/sql/language-manual/sql-ref-syntax-ddl-create-recipient
- Read data using Databricks-to-Databricks (recipient behavior): https://docs.databricks.com/aws/en/delta-sharing/read-data-databricks
- Monitor and manage Delta Sharing egress costs: https://docs.databricks.com/gcp/en/delta-sharing/manage-egress
- Lakehouse Federation / query federation overview: https://docs.databricks.com/aws/en/query-federation/database-federation
- Work with foreign tables (concepts/limitations): https://docs.databricks.com/gcp/en/tables/foreign

