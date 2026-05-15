# Section 05 Quiz: Data Governance & Quality (50 MCQs)

Instructions: Choose the one best answer (A, B, C, or D) for each question.

1. Which statement correctly distinguishes Unity Catalog managed tables from external tables?
   A. Dropping a managed table deletes both metadata and underlying data files; dropping an external table deletes metadata only.
   B. Dropping a managed table deletes metadata only; dropping an external table deletes both metadata and underlying data files.
   C. Both managed and external tables always delete underlying data files on `DROP TABLE`.
   D. Neither managed nor external tables ever delete underlying data files on `DROP TABLE`.

2. A team needs non-Databricks tools to directly read the underlying files in cloud object storage. Which table type is the best fit?
   A. Managed table
   B. External table
   C. Temporary view
   D. Materialized view

3. In Unity Catalog, which table type is described as the default and recommended type?
   A. External
   B. Foreign
   C. Managed
   D. Temporary

4. Which statement is true about non-Delta external tables in Unity Catalog?
   A. They do not provide Delta Lake transactional guarantees and performance optimizations.
   B. They automatically gain Delta Lake time travel and ACID guarantees.
   C. They can only be stored in Databricks-managed locations.
   D. They are required for Delta Sharing.

5. According to Databricks documentation, Unity Catalog supports which primary table types?
   A. Managed and temporary only
   B. External and temporary only
   C. Managed, external, and foreign
   D. External and foreign only

6. A user has `SELECT` on `main.sales.transactions` but receives a permission error when querying the table. Which additional privileges are commonly required to query a Unity Catalog table?
   A. `CREATE TABLE` on the schema and `MODIFY` on the table
   B. `USE CATALOG` on the catalog and `USE SCHEMA` on the schema
   C. `MANAGE` on the schema and `OWN` on the table
   D. `EXTERNAL USE SCHEMA` on the schema and `EXTERNAL USE LOCATION` on the external location

7. The `analysts` group already has `USE CATALOG` on `main` and `USE SCHEMA` on `main.sales`. Which SQL statement grants read-only access to the table `main.sales.transactions`?
   A. `GRANT READ ON SCHEMA main.sales TO analysts;`
   B. `GRANT USE SCHEMA ON TABLE main.sales.transactions TO analysts;`
   C. `GRANT MODIFY ON TABLE main.sales.transactions TO analysts;`
   D. `GRANT SELECT ON TABLE main.sales.transactions TO analysts;`

8. A developer group needs to create new tables in `main.sandbox`. Which privilege must be granted on the schema (in addition to `USE CATALOG` and `USE SCHEMA`)?
   A. `SELECT`
   B. `CREATE TABLE`
   C. `MODIFY`
   D. `READ VOLUME`

9. Which role is responsible for account-wide administration such as managing account settings, identities, workspaces, and creating Unity Catalog metastores?
   A. Account admin
   B. Workspace admin
   C. Table owner
   D. SQL warehouse admin

10. Which role is designed to manage governance at the Unity Catalog metastore level across workspaces attached to that metastore?
   A. Workspace admin
   B. Cluster admin
   C. Metastore admin
   D. Notebook owner

11. In Unity Catalog, what is true about object ownership?
   A. Ownership automatically inherits down to all child objects.
   B. The principal that creates an object becomes its initial owner.
   C. Only account admins can be owners of Unity Catalog objects.
   D. Owners cannot grant privileges unless they are also metastore admins.

12. Which privilege allows a principal to manage privileges on, transfer ownership of, and delete a Unity Catalog object without being the owner?
   A. `MANAGE`
   B. `SELECT`
   C. `USE SCHEMA`
   D. `BROWSE`

13. Which statement about `REVOKE` in Unity Catalog is correct?
   A. `REVOKE` fails if the privilege was never granted.
   B. `REVOKE` automatically drops the securable object.
   C. `REVOKE` requires the grantee to approve the revocation.
   D. A `REVOKE` statement can succeed even if the privileges were not granted.

14. A user can see a table name in a message but cannot resolve it in SQL using three-level namespace. Which missing privilege is the most likely cause?
   A. Missing `USE CATALOG` and/or `USE SCHEMA` on the table's parents
   B. Missing `CREATE TABLE` on the schema
   C. Missing `WRITE VOLUME` on a volume
   D. Missing `REFRESH` on the table

15. A data engineer wants a group to be able to insert, update, and delete rows in a Delta table `main.sales.transactions`. Which privilege is most appropriate?
   A. `BROWSE`
   B. `USE SCHEMA`
   C. `MODIFY`
   D. `CREATE TABLE`

16. Which SQL statement is valid for granting catalog usage in Unity Catalog?
   A. `GRANT USE ON CATALOG main TO finance;`
   B. `GRANT USAGE ON CATALOG main TO finance;`
   C. `GRANT USE CATALOG ON DATABASE main TO finance;`
   D. `GRANT USE CATALOG ON CATALOG main TO finance;`

17. Which statement about Unity Catalog ownership inheritance is correct?
   A. If you own a schema, you automatically own all tables in that schema.
   B. Ownership does not automatically inherit to child objects.
   C. If you own a catalog, you automatically own all objects in that catalog.
   D. Ownership inheritance depends only on the cloud provider (AWS/Azure/GCP).

18. A user wants to grant privileges on a table to another group. Which is the most correct requirement statement?
   A. The user must be a workspace admin.
   B. The user must be the table owner or have `MANAGE` on the table.
   C. The user must have `SELECT` on the table.
   D. The user must have `CREATE TABLE` on the parent schema.

19. A governance team wants to delegate permissions management on `main.sales` schema to a group without transferring ownership. Which grant best matches that requirement?
   A. `GRANT MODIFY ON SCHEMA main.sales TO governance;`
   B. `GRANT SELECT ON SCHEMA main.sales TO governance;`
   C. `GRANT MANAGE ON SCHEMA main.sales TO governance;`
   D. `GRANT CREATE TABLE ON SCHEMA main.sales TO governance;`

20. Which is the best "least privilege" grant set to allow a group to read a table `main.sales.transactions`?
   A. `USE CATALOG` + `USE SCHEMA` + `SELECT` on the table
   B. `CREATE TABLE` on schema + `MODIFY` on the table
   C. `ALL PRIVILEGES` on the metastore
   D. `MANAGE` on the catalog only

21. Where are audit logs stored for SQL querying when system tables are enabled?
   A. `system.access.audit`
   B. `hive_metastore.audit_logs`
   C. `information_schema.audit`
   D. `main.audit.logs`

22. When audit log delivery is configured, what is the format and storage location of delivered audit logs?
   A. CSV files stored in DBFS only
   B. JSON files delivered to a configured cloud storage destination, partitioned by workspace and date
   C. Parquet files stored in the driver local disk
   D. Delta tables automatically created in the `main` catalog

23. If audit log delivery is configured for the entire account, where are account-level events not associated with a single workspace delivered?
   A. They are discarded.
   B. They are delivered only to the "first" workspace created.
   C. They are delivered to the recipient's workspace.
   D. They are delivered under `workspaceId=0`.

24. In Databricks, where would you go to visually explore a table's lineage graph?
   A. Spark UI
   B. Jobs UI
   C. Catalog Explorer, Lineage tab (and lineage graph)
   D. Cluster event log

25. Which system tables are used to query Unity Catalog lineage programmatically?
   A. `system.security.table_history` and `system.security.column_history`
   B. `system.access.table_lineage` and `system.access.column_lineage`
   C. `system.billing.table_lineage` and `system.billing.column_lineage`
   D. `system.information_schema.table_lineage` and `system.information_schema.column_lineage`

26. Unity Catalog lineage is aggregated across which boundary?
   A. All workspaces attached to the same Unity Catalog metastore
   B. Only within a single notebook
   C. Only within a single job run
   D. Only within a single SQL warehouse

27. A user says, "I can query the table, but I can't see lineage details in Catalog Explorer." Which permission is often required to view lineage/metadata visibility on objects?
   A. `CREATE TABLE`
   B. `MODIFY`
   C. `WRITE VOLUME`
   D. `BROWSE`

28. A provider wants to share data with a partner that does not use Databricks. Which Delta Sharing type best fits?
   A. Open sharing
   B. Same-metastore sharing
   C. Databricks notebook sharing
   D. Workspace-local sharing

29. Which Delta Sharing type does not require a credential file for the recipient and can share assets like notebooks/volumes/models?
   A. Open sharing only
   B. Open-source server only
   C. Databricks-to-Databricks sharing
   D. Manual file export

30. In Delta Sharing, what is the container that a provider creates to group tables and other shared assets?
   A. Connection
   B. Share
   C. External location
   D. Foreign catalog

31. Which SQL statement creates a share in a Unity Catalog-enabled workspace?
   A. `CREATE CATALOG sales_share;`
   B. `CREATE EXTERNAL LOCATION sales_share;`
   C. `CREATE CONNECTION sales_share;`
   D. `CREATE SHARE sales_share;`

32. Which SQL statement adds a table to an existing share?
   A. `ALTER SHARE sales_share ADD TABLE main.sales.transactions;`
   B. `ALTER TABLE sales_share ADD SHARE main.sales.transactions;`
   C. `GRANT TABLE main.sales.transactions TO SHARE sales_share;`
   D. `INSERT INTO SHARE sales_share VALUES (main.sales.transactions);`

33. Two Databricks workspaces are attached to the same Unity Catalog metastore. What is the recommended way to share data access between them?
   A. Always use open Delta Sharing
   B. Always export data files and email them
   C. Use Unity Catalog permissions (GRANT) without Delta Sharing
   D. Use Lakehouse Federation connections

34. Which is a primary advantage of Delta Sharing compared to many traditional data-sharing approaches?
   A. It requires replicating full copies of data into the recipient's storage.
   B. It does not require data replication to share a single copy of data.
   C. It allows recipients to write back into the provider's tables.
   D. It disables auditing by default to reduce overhead.

35. Which statement is a fundamental limitation of Delta Sharing from the recipient's perspective?
   A. Recipients get read-only access to shared data.
   B. Recipients must own the provider's metastore.
   C. Recipients can only access data if they are account admins.
   D. Recipients can only query shared data from the provider's notebooks.

36. Which tabular formats are commonly required/supported for Delta Sharing providers to share?
   A. Only CSV and JSON
   B. Only Parquet
   C. Any Spark-supported format, always
   D. Delta and managed Iceberg table formats (per Delta Sharing documentation)

37. A user tries to add `main.sales.transactions` to a share and receives a permissions error. Which set of requirements is most accurate?
   A. The user must be a workspace admin and have `CREATE TABLE` on the schema.
   B. The user must be the share owner, have `USE SCHEMA` on the schema, and `SELECT` on the table (and keep it while shared).
   C. The user must have `MODIFY` on the table.
   D. The user must have `WRITE VOLUME` on a volume.

38. In Databricks-to-Databricks sharing, what is a common step a recipient team performs to make shared data available inside their workspace?
   A. Convert the shared tables to external tables.
   B. Turn on change data feed for all shared tables.
   C. Create a catalog from the share and then use Unity Catalog grants to assign access internally.
   D. Replace the provider's metastore with the recipient's metastore.

39. Which statement best captures the core cross-cloud cost consideration for Delta Sharing?
   A. Sharing within a region typically incurs no egress cost; sharing across regions or clouds can incur cloud vendor egress fees.
   B. Delta Sharing always incurs a fixed Databricks fee per share, regardless of region.
   C. Delta Sharing eliminates all cloud network charges by design.
   D. Delta Sharing requires full replication to avoid network charges.

40. When data is shared across regions/clouds and egress charges apply, who typically charges for that network egress?
   A. Databricks charges egress fees as part of DBUs.
   B. The recipient's BI tool vendor charges egress fees.
   C. The identity provider (IdP) charges egress fees.
   D. The cloud storage/network vendor charges egress fees.

41. A security analyst needs to review who created and updated Delta Sharing shares over time. Which data source is most appropriate?
   A. Unity Catalog lineage graph
   B. Spark event logs
   C. Audit logs (for example, `system.access.audit` when enabled)
   D. Cluster driver logs

42. A data engineer wants to join a table in an external operational database with a Delta table in Databricks without first ingesting the external data. Which feature best fits?
   A. Lakehouse Federation (query federation)
   B. Open Delta Sharing
   C. Managed tables
   D. Auto Loader

43. In Lakehouse Federation, which Unity Catalog object stores credentials and connection details for accessing an external database system?
   A. Share
   B. External table
   C. Connection
   D. Volume

44. Which SQL command creates a foreign catalog in Unity Catalog for Lakehouse Federation?
   A. `CREATE SHARE`
   B. `CREATE FOREIGN CATALOG`
   C. `CREATE VOLUME`
   D. `CREATE SCHEMA`

45. Which statement about foreign tables accessed through Lakehouse Federation (external sources) is most accurate?
   A. They behave exactly like managed Delta tables with full transactional guarantees.
   B. They automatically become managed tables after the first query.
   C. They can always be updated and deleted from Databricks.
   D. They are typically read-only and do not provide UC managed table transactional guarantees.

46. A user can query a table but cannot resolve its schema name unless they hardcode the full path. Which privilege is most directly tied to schema-level namespace usage?
   A. `USE SCHEMA`
   B. `SELECT`
   C. `CREATE FUNCTION`
   D. `READ VOLUME`

47. Which statement best describes why managed tables are often preferred for governance-first lakehouse designs?
   A. They require non-Databricks clients to access underlying files.
   B. Unity Catalog manages their lifecycle and storage location, and dropping the table deletes underlying files.
   C. They can only be queried from classic clusters, not SQL warehouses.
   D. They cannot participate in Unity Catalog permissions.

48. A provider wants to share curated tables with an external partner using Power BI, where the partner does not have a Databricks workspace. Which Delta Sharing type should the provider use?
   A. Databricks-to-Databricks sharing
   B. Open sharing
   C. Same-metastore sharing
   D. Lakehouse Federation

49. You need to answer: "Which user granted `SELECT` on `main.sales.transactions` to `analysts` last week?" Which feature is designed to provide that historical record?
   A. Lineage
   B. Audit logs
   C. Liquid clustering
   D. Auto Loader

50. Which is a known Lakehouse Federation behavior that can impact naming and object imports into Unity Catalog?
   A. Schema and table names are converted to lowercase in Unity Catalog, which can cause collisions.
   B. Foreign catalogs preserve all source object names exactly as-is without any normalization.
   C. Foreign catalogs require Delta Sharing tokens to query.
   D. Foreign tables always store data in Databricks-managed storage locations.
