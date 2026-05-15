# Section 05 Quiz Answers (Current exam through May 3, 2026)

1. A | Objective: Explain the difference between managed and external tables. | Rationale: Managed table drop deletes metadata and underlying files; external table drop removes metadata only.
2. B | Objective: Explain the difference between managed and external tables. | Rationale: External tables are used when you need to register data you manage and preserve direct file access patterns outside Databricks.
3. C | Objective: Explain the difference between managed and external tables. | Rationale: Managed tables are documented as the default and recommended table type in Unity Catalog.
4. A | Objective: Explain the difference between managed and external tables. | Rationale: Non-Delta external tables lack Delta Lake transactional guarantees and many optimizations.
5. C | Objective: Explain the difference between managed and external tables. | Rationale: Documentation describes managed, external, and foreign as the primary Unity Catalog table types.
6. B | Objective: Identify the grant of permissions to users and groups within UC. | Rationale: `SELECT` alone is insufficient; namespace privileges `USE CATALOG` and `USE SCHEMA` are typically required to resolve/query the table.
7. D | Objective: Identify the grant of permissions to users and groups within UC. | Rationale: Read-only table access is granted with `SELECT` on the table (assuming required `USE` privileges already exist).
8. B | Objective: Identify the grant of permissions to users and groups within UC. | Rationale: Creating tables in a schema requires `CREATE TABLE` on that schema (plus `USE` privileges).
9. A | Objective: Identify key roles in UC. | Rationale: Account admins manage account-wide settings and can create Unity Catalog metastores.
10. C | Objective: Identify key roles in UC. | Rationale: Metastore admins govern Unity Catalog objects at the metastore scope across attached workspaces.
11. B | Objective: Identify key roles in UC. | Rationale: Unity Catalog assigns ownership to the creator (initial owner), and ownership is a key governance role concept.
12. A | Objective: Identify the grant of permissions to users and groups within UC. | Rationale: `MANAGE` is the privilege used to delegate ownership-like management capabilities without changing ownership.
13. D | Objective: Identify the grant of permissions to users and groups within UC. | Rationale: `REVOKE` is idempotent; it ensures a privilege is not present even if it was never granted.
14. A | Objective: Identify the grant of permissions to users and groups within UC. | Rationale: Missing `USE CATALOG` and/or `USE SCHEMA` commonly prevents object name resolution in the three-level namespace.
15. C | Objective: Identify the grant of permissions to users and groups within UC. | Rationale: `MODIFY` is the standard Unity Catalog privilege for writing/updating/deleting table data.
16. D | Objective: Identify the grant of permissions to users and groups within UC. | Rationale: The Unity Catalog syntax is `GRANT USE CATALOG ON CATALOG <name> TO <principal>`.
17. B | Objective: Identify the grant of permissions to users and groups within UC. | Rationale: Ownership does not automatically inherit to child objects; you must grant privileges at the correct scope.
18. B | Objective: Identify the grant of permissions to users and groups within UC. | Rationale: Granting privileges requires ownership of the object or `MANAGE` on that object (not just `SELECT`).
19. C | Objective: Identify the grant of permissions to users and groups within UC. | Rationale: `MANAGE` on a schema delegates the ability to manage grants and related administration on that schema.
20. A | Objective: Identify the grant of permissions to users and groups within UC. | Rationale: Least-privilege read access typically requires `USE CATALOG`, `USE SCHEMA`, and `SELECT` on the table.
21. A | Objective: Identify how audit logs are stored. | Rationale: When system tables are enabled, audit logs are queryable in the system table `system.access.audit`.
22. B | Objective: Identify how audit logs are stored. | Rationale: Audit log delivery writes JSON audit log files to your configured cloud storage destination, partitioned by workspace and date.
23. D | Objective: Identify how audit logs are stored. | Rationale: Account-level events not tied to a single workspace can be delivered under `workspaceId=0`.
24. C | Objective: Use lineage features in Unity Catalog. | Rationale: Catalog Explorer provides a Lineage tab and an interactive lineage graph for Unity Catalog objects.
25. B | Objective: Use lineage features in Unity Catalog. | Rationale: Programmatic lineage querying uses `system.access.table_lineage` and `system.access.column_lineage`.
26. A | Objective: Use lineage features in Unity Catalog. | Rationale: Lineage is aggregated across all workspaces attached to the same Unity Catalog metastore.
27. D | Objective: Use lineage features in Unity Catalog. | Rationale: Lineage/metadata visibility typically requires at least `BROWSE` permissions on the object.
28. A | Objective: Identify types of delta sharing- Databricks vs external system. | Rationale: Open sharing is for recipients who may not be on Databricks.
29. C | Objective: Identify types of delta sharing- Databricks vs external system. | Rationale: Databricks-to-Databricks does not require a credential file and supports additional asset types compared to open sharing.
30. B | Objective: Use the Delta Sharing feature available with Unity Catalog to share data. | Rationale: Providers create a share to group the assets they want to share.
31. D | Objective: Use the Delta Sharing feature available with Unity Catalog to share data. | Rationale: `CREATE SHARE` creates the share object in a Unity Catalog-enabled workspace.
32. A | Objective: Use the Delta Sharing feature available with Unity Catalog to share data. | Rationale: Tables are added to a share using `ALTER SHARE <share> ADD TABLE <table>`.
33. C | Objective: Use the Delta Sharing feature available with Unity Catalog to share data. | Rationale: If both workspaces share the same metastore, Unity Catalog grants are sufficient; Delta Sharing is for cross-metastore sharing.
34. B | Objective: Identify the advantages and limitations of Delta sharing. | Rationale: Delta Sharing's core advantage is sharing without requiring replication of full copies of data.
35. A | Objective: Identify the advantages and limitations of Delta sharing. | Rationale: Shared data is read-only to recipients.
36. D | Objective: Identify the advantages and limitations of Delta sharing. | Rationale: Delta Sharing documentation describes Delta and managed Iceberg as key shareable tabular formats (with documented constraints).
37. B | Objective: Use the Delta Sharing feature available with Unity Catalog to share data. | Rationale: Adding a table to a share requires being the share owner and having/maintaining needed permissions such as `USE SCHEMA` and `SELECT` on the table.
38. C | Objective: Use the Delta Sharing feature available with Unity Catalog to share data. | Rationale: Recipients commonly create a catalog from a share and then use UC grants to distribute access internally.
39. A | Objective: Analyze the cost considerations of data sharing across clouds. | Rationale: Cross-region/cross-cloud sharing can incur vendor egress fees; same-region sharing typically avoids egress.
40. D | Objective: Analyze the cost considerations of data sharing across clouds. | Rationale: Network egress is charged by the underlying cloud storage/network provider, not as a Databricks DBU line item.
41. C | Objective: Identify how audit logs are stored. | Rationale: Share creation and access events are audited; `system.access.audit` is the designed query surface for audit history when enabled.
42. A | Objective: Identify Use cases of Lakehouse Federation when connected to external sources. | Rationale: Lakehouse Federation (query federation) supports querying and joining external sources without ingesting first.
43. C | Objective: Identify Use cases of Lakehouse Federation when connected to external sources. | Rationale: A UC connection stores the credentials/details needed to reach an external database system.
44. B | Objective: Identify Use cases of Lakehouse Federation when connected to external sources. | Rationale: Foreign catalogs are created using `CREATE FOREIGN CATALOG`.
45. D | Objective: Identify Use cases of Lakehouse Federation when connected to external sources. | Rationale: Foreign tables for external sources are generally read-only and do not provide managed-table transactional guarantees.
46. A | Objective: Identify the grant of permissions to users and groups within UC. | Rationale: `USE SCHEMA` is the schema-level privilege required to use the schema namespace.
47. B | Objective: Explain the difference between managed and external tables. | Rationale: Managed tables are lifecycle-managed by UC; dropping removes underlying data files as well as metadata.
48. B | Objective: Identify types of delta sharing- Databricks vs external system. | Rationale: Sharing to a non-Databricks consumer like Power BI without a Databricks workspace implies open sharing.
49. B | Objective: Identify how audit logs are stored. | Rationale: "Who granted access" is an audit question; audit logs provide the historical record of permission changes.
50. A | Objective: Identify Use cases of Lakehouse Federation when connected to external sources. | Rationale: Lakehouse Federation documentation notes that schema/table names are converted to lowercase in Unity Catalog, which can cause collisions.

