# Section 05: Data Governance & Quality (Current exam through May 3, 2026)

## Scope and Objectives (current-exam wording)
This section maps to the **CURRENT EXAM GUIDE** (live through **May 3, 2026**):

- Explain the difference between managed and external tables.
- Identify the grant of permissions to users and groups within UC.
- Identify key roles in UC.
- Identify how audit logs are stored.
- Use lineage features in Unity Catalog.
- Use the Delta Sharing feature available with Unity Catalog to share data.
- Identify the advantages and limitations of Delta sharing.
- Identify types of delta sharing- Databricks vs external system.
- Analyze the cost considerations of data sharing across clouds.
- Identify Use cases of Lakehouse Federation when connected to external sources.

## Terminology Drift Note (read this first)
- **Unity Catalog (UC)** is the current governance model for Databricks. Many docs use "securables" to mean governed objects like catalogs, schemas, tables, views, volumes, external locations, and more.
- Databricks docs often call **Lakehouse Federation** "**query federation**". In practice for exam prep: Lakehouse Federation = defining UC connections and querying external systems through **foreign catalogs/tables**.
- A **new exam version goes live May 4, 2026**. Topics like **ABAC**, **row filters**, **column masks**, and **DENY** can appear in newer governance material; treat them as terminology awareness only for this exam version (do not study them as objectives for the current exam).

## Core Concepts (what the exam expects you to be fluent in)
- **Table type decisions**
  - Managed tables: UC manages lifecycle and storage location; `DROP TABLE` removes metadata and underlying data files.
  - External tables: UC governs access, but you manage the data lifecycle and storage layout; `DROP TABLE` removes metadata only.
- **UC permissions (RBAC via GRANT)**
  - You almost always need `USE CATALOG` and `USE SCHEMA` plus an object privilege (for example `SELECT` on a table) to successfully access data.
  - Ownership and the `MANAGE` privilege determine who can grant permissions.
- **Audit and lineage**
  - Audit logs: know both "where you query them" (system table) and "where they can be delivered" (JSON log delivery destination).
  - Lineage: know where to view lineage (Catalog Explorer) and the system tables used to query lineage programmatically.
- **Delta Sharing**
  - Know the objects: provider, recipient, share, and (recipient-side) shared catalog.
  - Know the two "types" you'll be tested on: **open sharing** (external systems) vs **Databricks-to-Databricks**.
  - Know advantages and limitations, plus the big cost concept: **cross-region/cross-cloud egress fees**.
- **Lakehouse Federation**
  - Know "why": query and join external data sources without copying into your lakehouse.
  - Know "how": UC connections + foreign catalogs/tables; governed by UC like other securables.

## Decision Tables (quick recall)
### "I need to share data" decision
| Need | Best-fit feature | Why |
|---|---|---|
| Share read-only data to a partner on any platform | Delta Sharing (open sharing) | Open protocol; recipients can use connectors outside Databricks |
| Share data to another Databricks account / different UC metastore | Delta Sharing (Databricks-to-Databricks) | No recipient token file; Databricks-managed secure connection |
| Query external database live (no replication) and join with lakehouse data | Lakehouse Federation | Federated query via UC connection/foreign catalog |

### "I need a table" decision
| Need | Table type | Key exam cue |
|---|---|---|
| Default, recommended; UC-managed lifecycle | Managed | Dropping deletes data + metadata |
| Existing files you manage; or non-Delta formats | External | Dropping deletes metadata only |

## Common Exam Traps
- Confusing **workspace roles** (workspace admin) with **UC roles** (metastore admin, object owner) and with **grants** (privileges on securables).
- Forgetting that a user can have `SELECT` on a table but still fail to query it without `USE CATALOG` and `USE SCHEMA`.
- Mixing up **Delta Sharing** (data sharing protocol) with **Lakehouse Federation** (querying external sources through UC connections).
- Missing the cost angle: Delta Sharing avoids replication, but **egress** can be charged by the cloud vendor when data crosses regions/clouds.

## Official-Source Links (Databricks)
- Unity Catalog overview: https://docs.databricks.com/data-governance/unity-catalog/index.html
- Unity Catalog: manage privileges (GRANT/REVOKE): https://docs.databricks.com/aws/en/data-governance/unity-catalog/manage-privileges/
- Unity Catalog table types (managed vs external vs foreign): https://docs.databricks.com/aws/en/tables/delta-table
- Audit log delivery (JSON files to cloud storage): https://docs.databricks.com/aws/en/admin/account-settings/audit-log-delivery
- System tables (audit logs, lineage, etc): https://docs.databricks.com/aws/en/admin/system-tables
- View lineage in Unity Catalog: https://docs.databricks.com/en/data-governance/unity-catalog/data-lineage.html
- Delta Sharing overview: https://docs.databricks.com/aws/en/delta-sharing
- Delta Sharing egress costs: https://docs.databricks.com/gcp/en/delta-sharing/manage-egress
- Lakehouse Federation (query federation): https://docs.databricks.com/aws/en/query-federation/database-federation

