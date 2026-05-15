# Audit Logs and Lineage (Unity Catalog) (Current exam through May 3, 2026)

## Scope and Objectives (current-exam wording)
- Identify how audit logs are stored.
- Use lineage features in Unity Catalog.

## Terminology Drift Note
- Databricks provides two common ways you'll see audit logs discussed:
  - **Audit log system table** in the `system` catalog (query with SQL).
  - **Audit log delivery** (Databricks delivers JSON files to your cloud storage destination).
- "Lineage" appears in two places:
  - **Catalog Explorer** UI (Lineage tab and lineage graph).
  - **Lineage system tables** (query lineage programmatically).

## Core Concepts

### 1) Audit Logs: What They Are and Where They Are Stored
Audit logs are a historical record of user and system activity in Databricks for monitoring, compliance, and investigations.

For the exam, know both of these "storage" answers:

**A) System table (recommended in docs)**
- Audit logs can be accessed in Unity Catalog through the system table:
  - `system.access.audit`
- This is a Databricks-hosted analytical store inside the `system` catalog, designed for querying operational data.

**B) Log delivery (JSON files in your cloud storage)**
- You can configure audit log delivery so Databricks periodically writes audit log events as **JSON files** to a configured cloud storage destination.
- The path pattern includes `workspaceId=<workspaceId>` and `date=<yyyy-mm-dd>`. Account-level events not tied to one workspace can be delivered under `workspaceId=0`.

What questions often test:
- "Where do I query audit logs with SQL?": `system.access.audit`.
- "What format and where are delivered audit logs?": JSON files in your configured delivery bucket/container, partitioned by workspace and date.

### 2) Lineage: What It Captures and How You Use It
Unity Catalog lineage captures runtime lineage across queries run on Databricks:
- Supported for all languages and can capture down to **column-level**.
- Lineage relates tables to upstream/downstream sources, and can show related notebooks, jobs, and dashboards.
- Lineage is **aggregated across workspaces attached to the same metastore**.

Two ways to use lineage:

**A) Visualize lineage in Catalog Explorer**
- Find a table and open the **Lineage** tab.
- Use "See Lineage Graph" for an interactive graph.

**B) Query lineage using system tables**
- Table-level lineage: `system.access.table_lineage`
- Column-level lineage: `system.access.column_lineage`

Exam cue:
- If the question asks "how do I programmatically retrieve lineage?" the answer is "lineage system tables in the `system` catalog."

## Examples and Decision Tables

### A) Audit log delivery path recognition (pattern)
You do not need to memorize every provider's setup steps for the exam, but you should recognize the canonical delivery path shape:

```text
<bucket-or-container>/<delivery-path-prefix>/workspaceId=<workspaceId>/date=<yyyy-mm-dd>/auditlogs_<internal-id>.json
```

And know the special case:
- Account-level events can be delivered under `workspaceId=0` (when configured at the account level).

### B) Example: Find Unity Catalog permission changes (conceptual query)
Exact event names can vary by service and feature, but the exam expects you to know that you can query the audit log system table.

```sql
-- Example pattern: filter audit events for Unity Catalog-related actions
SELECT
  event_time,
  user_identity.email AS actor,
  service_name,
  action_name,
  request_params,
  response
FROM system.access.audit
WHERE service_name = 'unityCatalog'
ORDER BY event_time DESC;
```

### C) Example: Basic lineage query (table-level)
```sql
-- Example pattern: show upstream/downstream relationships at table level
SELECT
  source_table_full_name,
  target_table_full_name,
  event_time
FROM system.access.table_lineage
WHERE target_table_full_name = 'main.sales.transactions'
ORDER BY event_time DESC;
```

### D) Audit logs vs lineage: exam decision table
| If the question is asking about... | Think... | Best answer artifact |
|---|---|---|
| "Who changed permissions / who created a share / who ran a command?" | Auditability | `system.access.audit` (or delivered JSON audit logs) |
| "Where did this column come from / what tables feed this table?" | Data lineage | Catalog Explorer Lineage graph or `system.access.*_lineage` |

## Common Exam Traps
- Confusing **cluster/driver logs** (compute logs) with **audit logs** (governance/security event history). This objective is about audit logs, not Spark logs.
- Answering "audit logs are in Delta tables you create" instead of "audit logs are available via `system.access.audit` and/or JSON log delivery".
- Expecting lineage to cross boundaries it doesn't: lineage is metastore-scoped and depends on what Databricks can infer from runtime.
- Forgetting that lineage is a governance feature: object visibility/permissions (for example `BROWSE`) can affect whether you can see lineage details in the UI.

## Official-Source Links (Databricks)
- System tables (overview and available tables): https://docs.databricks.com/aws/en/admin/system-tables
- Audit log delivery (JSON to cloud storage): https://docs.databricks.com/aws/en/admin/account-settings/audit-log-delivery
- Audit log reference (events and schema): https://docs.databricks.com/en/admin/account-settings/audit-logs.html
- View lineage in Unity Catalog (UI + system tables): https://docs.databricks.com/en/data-governance/unity-catalog/data-lineage.html
- Lineage system tables reference: https://docs.databricks.com/aws/en/admin/system-tables/lineage
- Delta Sharing audit and monitoring (uses audit logs/system table): https://docs.databricks.com/gcp/en/delta-sharing/audit-logs

