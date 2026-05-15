# Unity Catalog (UC): Tables, Roles, and Permission Grants (Current exam through May 3, 2026)

## Scope and Objectives (current-exam wording)
- Explain the difference between managed and external tables.
- Identify the grant of permissions to users and groups within UC.
- Identify key roles in UC.

## Terminology Drift Note
- Databricks docs describe Unity Catalog permissions in terms of **securables** (catalog, schema, table, view, volume, external location, etc) and **privileges** (for example `USE CATALOG`, `USE SCHEMA`, `SELECT`, `MODIFY`, `MANAGE`).
- Post-May-4-2026 material may emphasize **ABAC**, **DENY**, **row filters**, and **column masks** more heavily. For the current exam, focus on **GRANT-based RBAC** and the common privilege patterns.

## Core Concepts

### 1) Table Types You'll Be Tested On: Managed vs External
Databricks documentation summarizes Unity Catalog table types as **managed**, **external**, and **foreign**. For this exam objective, the emphasis is managed vs external.

**Managed table**
- Default and recommended.
- Unity Catalog manages the storage location and data lifecycle.
- When you `DROP` a managed table, **both metadata and underlying data files are deleted**.

**External table**
- Registers data stored in cloud object storage that you manage.
- Unity Catalog governs access, but does not manage the data lifecycle or storage layout.
- When you `DROP` an external table, **only metadata is removed**; underlying files remain.
- External tables can be backed by Delta (recommended) or various non-Delta formats; non-Delta tables lack Delta's transactional guarantees and many optimizations.

Practical exam framing:
- "If I drop it, do I lose the files?" is the fastest way to distinguish managed vs external.
- "Need non-Databricks clients to directly access the underlying files?" usually points to external tables.

### 2) Key Roles in Unity Catalog (what they can do)
You'll see "roles" in two layers:

**Account-level admin roles**
- **Account admin**: manages account-wide settings, identities, workspaces, and Unity Catalog metastores.
- **Metastore admin**: manages metastore-level objects and governance at scale (for example can manage access broadly across workspaces attached to the metastore).

**Workspace-level role**
- **Workspace admin**: manages a single workspace (users, workspace settings, compute policies). Depending on how UC was enabled for the workspace, workspace admins may have additional default UC privileges.

**Data governance roles (object-level)**
- **Owner** (of a securable): every UC object has an owner; the creator becomes initial owner. Owners can perform all capabilities on the object (even if `ALL PRIVILEGES` is not explicitly listed in `SHOW GRANTS`).
- **Delegated manager** via `MANAGE`: a principal with `MANAGE` can grant/revoke privileges, transfer ownership, and delete the object without being the owner.

Exam cue:
- If the question is "who can grant privileges on X?" the answer is typically "the **owner** of X or a principal with **`MANAGE`** on X (or an admin role that can take ownership)."

### 3) How GRANT Works in UC (the patterns you must memorize)
Unity Catalog uses SQL `GRANT` / `REVOKE` statements (and a UI in Catalog Explorer) to assign privileges to **principals**:
- users
- service principals
- account-level groups

Typical access stack to query a table:
- `USE CATALOG` on the catalog
- `USE SCHEMA` on the schema
- `SELECT` on the table (or view)

If any one of these is missing, queries fail with permission errors.

## Examples and Decision Tables

### A) Managed vs External Table: Decision Table
| Question | Managed | External |
|---|---|---|
| Who manages the storage location and lifecycle? | Unity Catalog | You |
| `DROP TABLE` deletes data files? | Yes | No |
| Default / recommended? | Yes | Depends (use when needed) |
| Best when... | You want UC-managed lifecycle and governance-first design | You need to register existing data or keep direct file access patterns |

### B) "Read-only analyst" grant (very common exam scenario)
Scenario: A group should be able to read `main.sales.transactions`.

```sql
-- Usage privileges (namespace)
GRANT USE CATALOG ON CATALOG main TO `analyst_group`;
GRANT USE SCHEMA ON SCHEMA main.sales TO `analyst_group`;

-- Object privilege
GRANT SELECT ON TABLE main.sales.transactions TO `analyst_group`;
```

### C) "Developer can create tables in a schema" grant
Scenario: A group should be able to create tables in `main.sandbox`.

```sql
GRANT USE CATALOG ON CATALOG main TO `dev_group`;
GRANT USE SCHEMA ON SCHEMA main.sandbox TO `dev_group`;
GRANT CREATE TABLE ON SCHEMA main.sandbox TO `dev_group`;
```

### D) Delegating permission management without changing ownership
Scenario: You want a group to manage grants on a schema without making them owner.

```sql
GRANT MANAGE ON SCHEMA main.sales TO `data_governance_group`;
```

### E) Quick "required privileges" table
| Task | Minimum UC privileges (conceptual) |
|---|---|
| List objects in a catalog/schema in Catalog Explorer | `USE CATALOG` / `USE SCHEMA` (and sometimes `BROWSE` depending on object/feature) |
| Query a table | `USE CATALOG` + `USE SCHEMA` + `SELECT` |
| Insert/update/delete in a Delta table | `USE CATALOG` + `USE SCHEMA` + `MODIFY` |
| Create a table in a schema | `USE CATALOG` + `USE SCHEMA` + `CREATE TABLE` |
| Grant privileges on an object | object owner or `MANAGE` on that object |

## Common Exam Traps
- **"I granted SELECT, why can't they query?"** Because `USE CATALOG` and `USE SCHEMA` are also required to resolve the object namespace.
- Confusing **workspace-local groups** with **account-level groups**. Unity Catalog privileges are assigned to account-level principals; some workspace-local groups cannot be used as grantees.
- Assuming ownership is "inherited downward." In Unity Catalog, ownership does not automatically propagate to child objects; owners can manage/grant on children, but they do not automatically become the owner of child objects.
- Thinking `ALL PRIVILEGES` must show up for owners. Owners can do everything, but that capability is not always represented as an explicit `ALL PRIVILEGES` grant in `SHOW GRANTS`.

## Official-Source Links (Databricks)
- Unity Catalog overview and admin roles: https://docs.databricks.com/data-governance/unity-catalog/index.html
- UC permissions model concepts (ownership + `MANAGE`): https://docs.databricks.com/gcp/en/data-governance/unity-catalog/access-control/permissions-concepts
- Manage UC object ownership: https://docs.databricks.com/gcp/en/data-governance/unity-catalog/manage-privileges/ownership
- Manage privileges in UC (GRANT/REVOKE patterns): https://docs.databricks.com/aws/en/data-governance/unity-catalog/manage-privileges/
- UC privileges reference (full list by securable): https://docs.databricks.com/gcp/en/data-governance/unity-catalog/manage-privileges/privileges
- UC table types (managed vs external vs foreign): https://docs.databricks.com/aws/en/tables/delta-table

