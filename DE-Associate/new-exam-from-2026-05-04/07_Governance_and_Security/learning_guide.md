# Section 7: Governance and Security - Learning Guide

Exam guide version: NEW EXAM GUIDE, valid on or after 4-May-2026.

## Section Objectives

This section covers four syllabus bullets from the NEW EXAM GUIDE:

1. Differentiate between managed and external tables in Unity Catalog and perform basic operations such as create, modify, delete, and convert between managed and external tables.
2. Configure access controls using the UI and SQL by applying `GRANT`, `REVOKE`, and `DENY` privileges to principals at appropriate levels of the security hierarchy.
3. Understand column-level masking and row-level security to restrict data visibility based on user groups.
4. Understand Unity Catalog ABAC policies to centrally control row-level filtering and column masking for sensitive data.

## Important Current-Docs Note About `DENY`

The exam guide includes `DENY`, but current Databricks SQL documentation states that `DENY` is not supported by Unity Catalog and applies only to the `hive_metastore` catalog. For Unity Catalog-governed data, use `GRANT`, `REVOKE`, ownership, `MANAGE`, row filters, column masks, and ABAC policies.

Exam strategy:

- If the scenario is explicitly Unity Catalog, prefer `GRANT`/`REVOKE` and Unity Catalog policies.
- If a question asks about `DENY`, remember that it is not supported by Unity Catalog in current documentation.

## Unity Catalog Object Hierarchy

Unity Catalog uses a hierarchy:

```text
metastore
  catalog
    schema
      table
      view
      materialized view
      streaming table
      volume
      function
      model
```

Principals are users, groups, and service principals. Privileges can be granted to principals on securable objects.

Most table references use:

```text
catalog.schema.table
```

## Managed vs External Tables

### Managed Tables

Managed tables are the default and recommended table type for most Unity Catalog workloads. Unity Catalog manages both governance metadata and the storage lifecycle.

Characteristics:

- Unity Catalog determines the storage location.
- Data files are stored in managed storage for the metastore, catalog, or schema.
- Dropping the managed table removes both metadata and underlying data according to Databricks lifecycle behavior.
- Managed tables use Delta Lake or Apache Iceberg depending on support and configuration.
- Managed tables benefit from Unity Catalog optimizations and predictive optimization where supported.

Create a managed table:

```sql
CREATE TABLE de_academy.silver.orders (
  order_id STRING,
  customer_id STRING,
  amount DECIMAL(10,2)
);
```

Create from query:

```sql
CREATE OR REPLACE TABLE de_academy.gold.daily_sales AS
SELECT order_date, sum(amount) AS revenue
FROM de_academy.silver.orders
GROUP BY order_date;
```

### External Tables

External tables are registered in Unity Catalog, but their data files are stored at a cloud location that you manage.

Characteristics:

- Unity Catalog governs Databricks access and metadata.
- You specify the storage location.
- The location must be covered by a Unity Catalog external location.
- Dropping the table removes metadata from Unity Catalog but does not delete the underlying data files.
- External tables can use formats such as Delta, Parquet, CSV, JSON, Avro, and ORC.

Create an external table:

```sql
CREATE TABLE de_academy.bronze.orders_external (
  order_id STRING,
  customer_id STRING,
  amount DECIMAL(10,2)
)
LOCATION 's3://company-raw/orders/';
```

Exam focus:

- Managed table: UC manages storage lifecycle.
- External table: you manage underlying storage lifecycle.
- Both are governed by Unity Catalog when registered.
- External tables require governed external locations/storage credentials.

## Basic Table Operations

### Modify a Table

Add a column:

```sql
ALTER TABLE de_academy.silver.orders
ADD COLUMN order_status STRING;
```

Rename a column:

```sql
ALTER TABLE de_academy.silver.orders
RENAME COLUMN order_status TO status;
```

Set table properties:

```sql
ALTER TABLE de_academy.silver.orders
SET TBLPROPERTIES ('quality' = 'silver');
```

### Delete a Table

```sql
DROP TABLE de_academy.silver.orders;
```

Managed table effect:

- Table metadata is removed.
- Underlying managed data is removed according to platform lifecycle.

External table effect:

- Table metadata is removed.
- Underlying external files remain in your cloud storage.

### Convert External to Managed

A common approach is to create a managed table from the external table data:

```sql
CREATE TABLE de_academy.silver.orders_managed AS
SELECT *
FROM de_academy.bronze.orders_external;
```

Databricks also provides dedicated conversion workflows for external-to-managed Unity Catalog tables where supported. Always verify storage cleanup and retention behavior.

Exam focus:

- CTAS copies data into a managed table.
- Dropping an external table does not delete the source files.
- Converting requires thinking about data movement and cleanup.

## Access Control with SQL

### Required Access Pattern

To query a table, a principal typically needs:

- `USE CATALOG` on the catalog.
- `USE SCHEMA` on the schema.
- `SELECT` on the table or inherited SELECT from a parent object.

Example:

```sql
GRANT USE CATALOG ON CATALOG de_academy TO `data-analysts`;
GRANT USE SCHEMA ON SCHEMA de_academy.gold TO `data-analysts`;
GRANT SELECT ON TABLE de_academy.gold.daily_sales TO `data-analysts`;
```

Revoke access:

```sql
REVOKE SELECT ON TABLE de_academy.gold.daily_sales FROM `data-analysts`;
```

Show grants:

```sql
SHOW GRANTS ON TABLE de_academy.gold.daily_sales;
```

### Common Privileges

| Privilege | Common use |
| --- | --- |
| `USE CATALOG` | Allows access to objects inside a catalog when combined with child privileges. |
| `USE SCHEMA` | Allows access to objects inside a schema when combined with object privileges. |
| `SELECT` | Read table or view data. |
| `MODIFY` | Insert, update, and delete table data. |
| `CREATE TABLE` | Create tables in a schema. |
| `CREATE VOLUME` | Create volumes in a schema. |
| `CREATE FUNCTION` | Create functions in a schema. |
| `BROWSE` | Discover objects and request access. |
| `MANAGE` | Manage privileges, transfer ownership, and delete objects without being owner, with important limitations. |

Exam focus:

- `ALL PRIVILEGES` does not include `MANAGE`, `EXTERNAL USE SCHEMA`, or `EXTERNAL USE LOCATION`.
- `MANAGE` is powerful and should be granted carefully.
- Grant privileges at the narrowest useful scope.
- Groups are preferred over individual users for scalable permissions.
- Service principals are used for automated workloads.

## Access Control in the UI

Catalog Explorer can manage permissions:

```text
Catalog -> choose object -> Permissions tab -> Grant or Revoke
```

Use the UI when:

- You need an interactive view of current permissions.
- You are granting access to a specific object.
- You are reviewing ownership or access requests.

Use SQL/CLI/IaC when:

- Access changes must be repeatable.
- You are automating deployments.
- You need reviewable change control.

## Row-Level Security

Row filters restrict which rows a user can see at query time. A row filter evaluates a function or policy condition and only returns allowed rows.

Use cases:

- Regional managers see only rows for their region.
- Sales teams see only assigned accounts.
- Contractors see only rows for approved projects.

Manual row filter concept:

```sql
CREATE FUNCTION de_academy.security.can_see_region(region STRING)
RETURN is_account_group_member(concat('region_', region));
```

Then apply a row filter through table policies or ABAC, depending on the chosen governance approach.

Exam focus:

- Row filters restrict rows.
- They do not grant base table access by themselves.
- Users still need object-level privileges such as `SELECT`.

## Column-Level Masking

Column masks transform sensitive column values at query time.

Use cases:

- Mask SSN except for compliance users.
- Show full email only to customer support.
- Redact phone numbers for analysts.

Mask function concept:

```sql
CREATE FUNCTION de_academy.security.mask_email(email STRING)
RETURN CASE
  WHEN is_account_group_member('pii_readers') THEN email
  ELSE regexp_replace(email, '(^.).*(@.*$)', '$1***$2')
END;
```

Exam focus:

- Column masks control values in columns.
- The returned value must be compatible with the masked column type.
- Column masks do not replace table privileges; they add restrictions on top of access.

## ABAC Policies

Attribute-based access control, or ABAC, applies policies based on attributes associated with securables. In Unity Catalog, those attributes are governed tags.

ABAC policy types:

- Row filter policies.
- Column mask policies.

Why ABAC matters:

- Centralized policy definition.
- Policies can attach at catalog, schema, or table scope.
- Governed tags determine which objects or columns the policy applies to.
- Table owners cannot remove higher-level ABAC policies.
- Easier to scale than manually configuring every table.

Example concepts:

```text
Tag: sensitivity = pii
Policy: mask columns tagged sensitivity=pii unless user is in pii_readers
Scope: catalog de_academy
```

Exam focus:

- ABAC is centralized and tag-driven.
- ABAC does not grant base access; users still need grants.
- ABAC policies can apply to tables, materialized views, and streaming tables where supported.
- If conflicting distinct row filters or masks apply, access can be blocked.

## ABAC vs Manual Row Filters and Masks

| Requirement | Best fit |
| --- | --- |
| Small number of tables with custom logic | Manual table-level row filter or column mask |
| Many tables/columns with common sensitivity tags | ABAC |
| Central governance team needs non-bypassable policy | ABAC at catalog/schema level |
| Need a read-only object with custom query logic | Dynamic view |

Exam focus:

- Dynamic views create a separate object.
- Row filters and column masks apply directly to tables/columns.
- ABAC uses governed tags and centralized policies.

## Common Exam Traps

- Granting `SELECT` on a table without `USE CATALOG` and `USE SCHEMA`.
- Using individual user grants instead of groups for team access.
- Assuming `DENY` works in Unity Catalog despite current docs saying it does not.
- Assuming row filters or masks grant access by themselves.
- Dropping an external table and expecting cloud files to be deleted.
- Dropping a managed table without understanding data lifecycle impact.
- Confusing external tables with foreign tables. External tables are files in cloud storage; foreign tables are query federation objects.
- Using table-level masks on hundreds of tables when ABAC tags would centralize policy.

## Mini Labs

### Lab 1: Managed vs External

```sql
CREATE TABLE de_academy.silver.customers (
  customer_id STRING,
  email STRING
);

CREATE TABLE de_academy.bronze.customers_external (
  customer_id STRING,
  email STRING
)
LOCATION 's3://company-raw/customers/';
```

### Lab 2: Grants

```sql
GRANT USE CATALOG ON CATALOG de_academy TO `analysts`;
GRANT USE SCHEMA ON SCHEMA de_academy.gold TO `analysts`;
GRANT SELECT ON TABLE de_academy.gold.daily_sales TO `analysts`;

SHOW GRANTS ON TABLE de_academy.gold.daily_sales;

REVOKE SELECT ON TABLE de_academy.gold.daily_sales FROM `analysts`;
```

### Lab 3: Policy Selection

| Scenario | Control |
| --- | --- |
| Analysts need read-only access to a table | `GRANT SELECT` plus parent `USE` privileges |
| Regional users should see only their rows | Row filter |
| PII column should be redacted | Column mask |
| All columns tagged `sensitivity=pii` across a catalog should be masked | ABAC column mask policy |

## Official References

- Databricks Certified Data Engineer Associate Exam Guide, May 4, 2026: https://www.databricks.com/sites/default/files/2026-03/databricks-certified-data-engineer-associate-exam-guide-may-4-2026.pdf
- What is Unity Catalog?: https://docs.databricks.com/en/data-governance/unity-catalog/index.html
- Unity Catalog securable objects: https://docs.databricks.com/aws/en/data-governance/unity-catalog/securable-objects
- Manage privileges in Unity Catalog: https://docs.databricks.com/en/data-governance/unity-catalog/manage-privileges/index.html
- Unity Catalog privileges reference: https://docs.databricks.com/aws/en/data-governance/unity-catalog/manage-privileges/privileges
- GRANT SQL reference: https://docs.databricks.com/aws/en/sql/language-manual/security-grant
- REVOKE SQL reference: https://docs.databricks.com/aws/en/sql/language-manual/security-revoke
- DENY SQL reference: https://docs.databricks.com/gcp/en/sql/language-manual/security-deny
- Databricks Unity Catalog table types: https://docs.databricks.com/aws/en/tables/delta-table
- Managed versus external assets in Unity Catalog: https://docs.databricks.com/aws/en/data-governance/unity-catalog/managed-versus-external
- Convert an external table to a managed Unity Catalog table: https://docs.databricks.com/aws/en/tables/convert-external-managed
- Row filters and column masks: https://docs.databricks.com/aws/en/data-governance/unity-catalog/filters-and-masks/
- Manually apply row filters and column masks: https://docs.databricks.com/aws/en/data-governance/unity-catalog/filters-and-masks/manually-apply
- Attribute-based access control in Unity Catalog: https://docs.databricks.com/aws/en/data-governance/unity-catalog/abac/
- ABAC vs table-level row filters and column masks: https://docs.databricks.com/aws/en/data-governance/unity-catalog/abac/abac-vs-rls-cm
- Create and manage ABAC policies: https://docs.databricks.com/aws/en/data-governance/unity-catalog/abac/policies

