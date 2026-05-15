# Section 7: Governance and Security - Practice Exam

Exam guide version: NEW EXAM GUIDE, valid on or after 4-May-2026.

Question style: multiple-choice with four plausible options, modeled after the sample questions in the attached guide. These are original practice questions, not official exam questions.

## Questions

**Question 1**  
**Objective:** Differentiate managed and external tables  
A table is created in Unity Catalog without a `LOCATION` clause, and Unity Catalog controls the storage location and lifecycle. What type of table is it?

A. Managed table  
B. External table  
C. Foreign table  
D. Temporary view

**Question 2**  
**Objective:** Differentiate managed and external tables  
A table is registered in Unity Catalog using data files stored at a company-managed cloud storage path. What type of table is it?

A. External table  
B. Managed table  
C. SQL warehouse  
D. Service principal

**Question 3**  
**Objective:** Differentiate managed and external tables  
What happens to underlying files when an external table is dropped?

A. Unity Catalog removes table metadata, but underlying cloud files remain.  
B. Unity Catalog always deletes all cloud files immediately.  
C. The metastore is deleted.  
D. All users receive SELECT.

**Question 4**  
**Objective:** Differentiate managed and external tables  
What happens when a managed table is dropped?

A. Metadata is removed and managed data is removed according to Databricks lifecycle behavior.  
B. Only notebook output is deleted.  
C. The external source system is dropped.  
D. The SQL warehouse is deleted.

**Question 5**  
**Objective:** Create managed tables  
Which SQL statement creates a managed table in Unity Catalog?

A. `CREATE TABLE catalog.schema.orders (order_id STRING, amount DECIMAL(10,2))`  
B. `CREATE TABLE catalog.schema.orders LOCATION 's3://raw/orders/'`  
C. `CREATE WAREHOUSE catalog.schema.orders`  
D. `GRANT TABLE catalog.schema.orders`

**Question 6**  
**Objective:** Create external tables  
Which SQL pattern creates an external table?

A. `CREATE TABLE catalog.schema.orders_ext (...) LOCATION 's3://company-raw/orders/'`  
B. `CREATE TABLE catalog.schema.orders (...)` with no location  
C. `CREATE BRANCH orders_ext`  
D. `CREATE MASK orders_ext`

**Question 7**  
**Objective:** Modify tables  
A data engineer needs to add a `status` column to an existing table. Which command pattern is correct?

A. `ALTER TABLE catalog.schema.orders ADD COLUMN status STRING`  
B. `GRANT COLUMN status TO orders`  
C. `DROP TABLE status FROM orders`  
D. `DENY COLUMN status`

**Question 8**  
**Objective:** Modify tables  
A data engineer needs to rename column `cust_id` to `customer_id`. Which command pattern is correct?

A. `ALTER TABLE catalog.schema.customers RENAME COLUMN cust_id TO customer_id`  
B. `CREATE TABLE customer_id RENAME cust_id`  
C. `REVOKE cust_id ON customer_id`  
D. `OPTIMIZE COLUMN cust_id`

**Question 9**  
**Objective:** Convert between table types  
A team wants to copy data from an external table into a managed Unity Catalog table. Which pattern is appropriate?

A. `CREATE TABLE catalog.schema.managed_orders AS SELECT * FROM catalog.schema.external_orders`  
B. `GRANT MANAGED ON external_orders`  
C. `DENY EXTERNAL ON external_orders`  
D. `SHOW GRANTS ON external_orders`

**Question 10**  
**Objective:** Configure access controls with GRANT  
An analyst group needs read access to `de.gold.daily_sales`. They already have `USE CATALOG` and `USE SCHEMA`. Which command grants table read access?

A. `GRANT SELECT ON TABLE de.gold.daily_sales TO analysts`  
B. `GRANT MODIFY ON TABLE de.gold.daily_sales TO analysts`  
C. `GRANT CREATE TABLE ON TABLE de.gold.daily_sales TO analysts`  
D. `GRANT DROP ON TABLE de.gold.daily_sales TO analysts`

**Question 11**  
**Objective:** Configure access controls with GRANT  
A group needs to discover and query tables in schema `de.gold`. Which parent privileges are typically required in addition to object access?

A. `USE CATALOG` on `de` and `USE SCHEMA` on `de.gold`  
B. `MODIFY` on all tables only  
C. `CREATE BRANCH` on the catalog  
D. `DENY SELECT` on the schema

**Question 12**  
**Objective:** Configure access controls with REVOKE  
A contractor group should no longer read `de.gold.daily_sales`. Which command removes the explicit read grant?

A. `REVOKE SELECT ON TABLE de.gold.daily_sales FROM contractors`  
B. `DROP TABLE contractors`  
C. `DELETE FROM de.gold.daily_sales WHERE group='contractors'`  
D. `OPTIMIZE de.gold.daily_sales FROM contractors`

**Question 13**  
**Objective:** Configure access controls with SQL  
Which command displays current grants on a table?

A. `SHOW GRANTS ON TABLE de.gold.daily_sales`  
B. `SHOW FILES ON TABLE de.gold.daily_sales`  
C. `SHOW BRANCHES ON TABLE de.gold.daily_sales`  
D. `SHOW TASKS ON TABLE de.gold.daily_sales`

**Question 14**  
**Objective:** Configure access controls with principals  
Which objects are principals in Unity Catalog access control?

A. Users, groups, and service principals  
B. Tables, views, and schemas only  
C. Spark stages only  
D. Delta files only

**Question 15**  
**Objective:** Configure access controls at hierarchy levels  
A permission granted on a catalog can apply to child schemas and objects through which concept?

A. Privilege inheritance  
B. Data skew  
C. Liquid clustering  
D. File arrival trigger

**Question 16**  
**Objective:** Configure access controls using groups  
Why are groups preferred over individual user grants for team access?

A. They scale better and simplify access management.  
B. They disable Unity Catalog.  
C. They automatically optimize tables.  
D. They remove the need for `USE CATALOG`.

**Question 17**  
**Objective:** Configure access controls with service principals  
Which principal type is commonly used for automated jobs or CI/CD deployments?

A. Service principal  
B. Notebook cell  
C. Spark stage  
D. Dashboard widget

**Question 18**  
**Objective:** Configure access controls with DENY  
According to current Databricks SQL documentation, what is true about `DENY` and Unity Catalog?

A. `DENY` is not supported by Unity Catalog and applies only to `hive_metastore`.  
B. `DENY` is the preferred Unity Catalog command for all access.  
C. `DENY` automatically creates row filters.  
D. `DENY` converts external tables to managed tables.

**Question 19**  
**Objective:** Configure access controls with GRANT/REVOKE  
If a user has `SELECT` on a table but lacks `USE SCHEMA` on the parent schema, what is likely?

A. The user still cannot query the table until parent usage privileges are granted.  
B. The user can always query any table.  
C. The table becomes external.  
D. The metastore is deleted.

**Question 20**  
**Objective:** Understand MANAGE privilege  
Which statement about `MANAGE` is correct?

A. It allows managing privileges and ownership-like operations but is not included in `ALL PRIVILEGES`.  
B. It is required to run every SELECT query.  
C. It is the same as predictive optimization.  
D. It applies only to dashboards.

**Question 21**  
**Objective:** Understand `ALL PRIVILEGES`  
Which privileges are not included in Unity Catalog `ALL PRIVILEGES` according to current docs?

A. `MANAGE`, `EXTERNAL USE SCHEMA`, and `EXTERNAL USE LOCATION`  
B. `SELECT` only  
C. `USE CATALOG` only  
D. `CREATE TABLE` only

**Question 22**  
**Objective:** Understand column-level masking  
A table contains SSNs. Analysts should query the table but see redacted SSNs unless they are in the compliance group. Which feature fits?

A. Column mask  
B. File arrival trigger  
C. Liquid clustering  
D. `VACUUM`

**Question 23**  
**Objective:** Understand row-level security  
Regional managers should see only rows for their assigned region. Which feature fits?

A. Row filter  
B. Column mask only  
C. `OPTIMIZE`  
D. Git branch

**Question 24**  
**Objective:** Understand row filters and masks  
Do row filters and column masks grant base access to a table by themselves?

A. No. Users still need object-level privileges such as `SELECT`.  
B. Yes. They replace all grants.  
C. Yes. They grant `MANAGE`.  
D. Yes. They create a SQL warehouse.

**Question 25**  
**Objective:** Understand column masks  
A column mask function should return what kind of value?

A. A value compatible with the masked column's data type.  
B. Always a BOOLEAN regardless of column type.  
C. Always a table.  
D. Always a Git branch name.

**Question 26**  
**Objective:** Understand row filters  
A row filter function generally determines:

A. Whether each row should be visible to the querying user.  
B. Which cluster should start.  
C. Which bundle target should deploy.  
D. Which files should be compacted.

**Question 27**  
**Objective:** Understand ABAC policies  
What does ABAC use to determine which data a policy should protect?

A. Governed tags and policy conditions.  
B. Notebook font size.  
C. Spark executor hostname.  
D. Dashboard refresh interval.

**Question 28**  
**Objective:** Understand ABAC policies  
Where can ABAC policies be attached?

A. Catalog, schema, or table scope, depending on policy design.  
B. Only notebook output.  
C. Only local files.  
D. Only Spark UI stages.

**Question 29**  
**Objective:** Understand ABAC policies  
Which policy types does Unity Catalog ABAC support?

A. Row filter policies and column mask policies.  
B. Git merge policies only.  
C. Cluster startup policies only.  
D. Dashboard color policies only.

**Question 30**  
**Objective:** Understand ABAC vs manual masks  
A governance team needs one centrally managed rule to mask all columns tagged `sensitivity=pii` across a catalog. Which approach is best?

A. ABAC column mask policy using governed tags.  
B. Manual mask on one table only.  
C. Local file upload.  
D. SQL warehouse rename.

**Question 31**  
**Objective:** Understand ABAC vs table-level controls  
A table owner manually adds a mask to one sensitive table. What is this approach?

A. Table-level column mask.  
B. Predictive optimization.  
C. File arrival trigger.  
D. Git branch protection.

**Question 32**  
**Objective:** Understand ABAC policy enforcement  
If conflicting distinct ABAC row filters apply to the same user and table, what can happen?

A. Databricks can block access and return an error.  
B. Databricks always ignores all policies.  
C. The table becomes unmanaged.  
D. The user receives `MANAGE`.

**Question 33**  
**Objective:** Understand ABAC and base privileges  
A user is in scope for an ABAC policy but has no `SELECT` on the table. Can the ABAC policy alone let them query the table?

A. No. ABAC restricts access but does not grant base table privileges.  
B. Yes. ABAC always grants `SELECT`.  
C. Yes. ABAC grants `USE CATALOG`.  
D. Yes. ABAC creates a dynamic view.

**Question 34**  
**Objective:** Configure access controls with UI  
Where can a user interactively grant permissions in the Databricks workspace?

A. Catalog Explorer Permissions tab.  
B. Spark UI Stage page.  
C. Notebook output display only.  
D. Git branch dropdown only.

**Question 35**  
**Objective:** Configure access controls with SQL  
Which command grants schema usage?

A. `GRANT USE SCHEMA ON SCHEMA de.gold TO analysts`  
B. `GRANT SELECT SCHEMA de.gold FROM analysts`  
C. `REVOKE USE SCHEMA ON SCHEMA de.gold TO analysts`  
D. `OPTIMIZE SCHEMA de.gold`

**Question 36**  
**Objective:** Configure access controls with SQL  
Which command grants catalog usage?

A. `GRANT USE CATALOG ON CATALOG de TO analysts`  
B. `GRANT CATALOG ON USE de TO analysts`  
C. `CREATE CATALOG USE analysts`  
D. `DENY CATALOG de`

**Question 37**  
**Objective:** Configure write access  
A data engineering group needs to insert and update rows in a table. Which privilege is relevant?

A. `MODIFY` on the table, with required parent usage and any additional required privileges.  
B. `BROWSE` only.  
C. `DENY SELECT`.  
D. `CLUSTER BY AUTO`.

**Question 38**  
**Objective:** Configure discovery access  
Which privilege allows users to discover metadata and request access without necessarily being able to query data?

A. `BROWSE`  
B. `MODIFY`  
C. `VACUUM`  
D. `COLLECT`

**Question 39**  
**Objective:** External table governance  
What Unity Catalog object commonly maps a cloud storage URI to a credential for external access?

A. External location  
B. Notebook widget  
C. Spark task  
D. Dashboard

**Question 40**  
**Objective:** External table governance  
What object abstracts cloud storage credentials in Unity Catalog?

A. Storage credential  
B. SQL warehouse  
C. Pull request  
D. Spark accumulator

**Question 41**  
**Objective:** Managed vs external assets  
Which table type is generally recommended for most Databricks use cases to benefit from UC optimizations?

A. Managed table  
B. External table always  
C. Foreign table always  
D. Temporary view only

**Question 42**  
**Objective:** Managed vs external assets  
Which statement about external tables is correct?

A. Unity Catalog governs access, but the underlying data lifecycle is managed outside Databricks.  
B. Unity Catalog always owns and deletes the files on drop.  
C. External tables cannot be queried.  
D. External tables are the same as SQL warehouses.

**Question 43**  
**Objective:** Managed vs external assets  
Which statement about managed tables is correct?

A. Unity Catalog controls governance and underlying storage lifecycle.  
B. Users must always specify a cloud `LOCATION`.  
C. Dropping the table leaves all managed files forever.  
D. Managed tables are not governed.

**Question 44**  
**Objective:** Row-level security vs column masking  
A policy should hide entire rows for unauthorized regions. Which control is correct?

A. Row filter  
B. Column mask  
C. `OPTIMIZE`  
D. Git push

**Question 45**  
**Objective:** Row-level security vs column masking  
A policy should show all rows but replace email values with redacted text for most users. Which control is correct?

A. Column mask  
B. Row filter  
C. File arrival trigger  
D. Table update trigger

**Question 46**  
**Objective:** Dynamic views vs filters and masks  
A team wants a separate read-only object that joins multiple tables and applies custom SQL logic. Which object can be appropriate?

A. Dynamic view or regular view with security logic.  
B. Predictive optimization.  
C. Spark executor.  
D. Bundle target.

**Question 47**  
**Objective:** ABAC central policy selection  
Why is ABAC preferred over manual masks for many tagged sensitive columns?

A. It centralizes policy management and applies dynamically based on governed tags.  
B. It removes the need for all table privileges.  
C. It is a Spark shuffle optimization.  
D. It creates Git branches.

**Question 48**  
**Objective:** Access controls integrated scenario  
A group needs read-only access to `de.gold.daily_sales`. Which minimal privilege set is most appropriate?

A. `USE CATALOG` on `de`, `USE SCHEMA` on `de.gold`, and `SELECT` on the table.  
B. `MODIFY` and `MANAGE` on the table only.  
C. `DENY SELECT` on the table.  
D. `CREATE TABLE` on the metastore only.

**Question 49**  
**Objective:** Governance and Security integrated scenario  
A catalog contains many tables with columns tagged `sensitivity=pii`. Analysts should query approved tables but see masked PII unless they are in `pii_readers`. Which design is best?

A. Grant base access as needed, then apply an ABAC column mask policy based on governed tags and group membership.  
B. Grant `ALL PRIVILEGES` to all analysts and skip masking.  
C. Drop all PII columns from source storage.  
D. Use file arrival triggers to hide columns.

**Question 50**  
**Objective:** Governance and Security integrated scenario  
A company wants to register data stored in its own cloud bucket, let Unity Catalog govern access, and keep lifecycle control of the files outside Databricks. What should it create?

A. External table using an external location and storage credential.  
B. Managed table with no location.  
C. SQL warehouse only.  
D. ABAC policy only with no table.

## Answer Key

**Question 1: A**  
A is correct because no `LOCATION` and UC-controlled storage indicates a managed table. B requires externally managed storage. C is federation. D is not a persistent table.

**Question 2: A**  
A is correct because external tables reference data at an external cloud path. B is UC-managed storage. C and D are not table types.

**Question 3: A**  
A is correct because external table drop removes metadata but leaves files. B describes managed behavior incorrectly. C and D are unrelated.

**Question 4: A**  
A is correct because managed table drop removes metadata and managed data per lifecycle behavior. B, C, and D are unrelated.

**Question 5: A**  
A is correct because no location creates a managed table. B specifies external storage. C and D are invalid for table creation.

**Question 6: A**  
A is correct because a `LOCATION` clause registers external storage. B creates managed. C and D are unrelated.

**Question 7: A**  
A is correct because `ALTER TABLE ... ADD COLUMN` modifies schema. B, C, and D are invalid.

**Question 8: A**  
A is correct because `ALTER TABLE ... RENAME COLUMN` renames a column. B, C, and D are invalid.

**Question 9: A**  
A is correct because CTAS copies data into a managed table when no external location is specified. B, C, and D do not convert/copy data.

**Question 10: A**  
A is correct because `SELECT` grants read access. B grants write capability. C is schema-level creation. D is not a normal table read grant.

**Question 11: A**  
A is correct because parent usage privileges are required to access child objects. B is write access only. C and D are unrelated/wrong.

**Question 12: A**  
A is correct because `REVOKE SELECT ... FROM` removes the explicit grant. B, C, and D do not remove a permission grant.

**Question 13: A**  
A is correct because `SHOW GRANTS` displays grants. B, C, and D are invalid for grants.

**Question 14: A**  
A is correct because principals include users, groups, and service principals. B lists securable objects. C and D are runtime/storage artifacts.

**Question 15: A**  
A is correct because UC privileges inherit downward in the hierarchy. B, C, and D are unrelated.

**Question 16: A**  
A is correct because groups simplify team access management. B, C, and D are false.

**Question 17: A**  
A is correct because service principals are used for automation. B, C, and D are not principals for automation.

**Question 18: A**  
A is correct according to current docs. B, C, and D are false.

**Question 19: A**  
A is correct because object access requires parent usage privileges. B, C, and D are false.

**Question 20: A**  
A is correct because `MANAGE` is powerful and not included in `ALL PRIVILEGES`. B, C, and D are false.

**Question 21: A**  
A is correct because those privileges are excluded from `ALL PRIVILEGES`. B, C, and D are incorrect.

**Question 22: A**  
A is correct because column masks redact column values. B, C, and D are unrelated.

**Question 23: A**  
A is correct because row filters restrict rows visible to users. B masks values, not rows. C and D are unrelated.

**Question 24: A**  
A is correct because filters and masks restrict access but do not grant it. B, C, and D are false.

**Question 25: A**  
A is correct because masks must return a compatible type. B, C, and D are wrong.

**Question 26: A**  
A is correct because row filters evaluate row visibility. B, C, and D are unrelated.

**Question 27: A**  
A is correct because ABAC uses governed tags and policy conditions. B, C, and D are unrelated.

**Question 28: A**  
A is correct because policies can attach at hierarchy scopes. B, C, and D are invalid.

**Question 29: A**  
A is correct because ABAC supports row filter and column mask policies. B, C, and D are unrelated.

**Question 30: A**  
A is correct because ABAC centralizes tag-based masking. B covers only one table. C and D are unrelated.

**Question 31: A**  
A is correct because a manually applied mask is table-level masking. B, C, and D are unrelated.

**Question 32: A**  
A is correct because conflicting distinct policies can block access. B, C, and D are false.

**Question 33: A**  
A is correct because ABAC is restrictive, not a grant. B, C, and D are false.

**Question 34: A**  
A is correct because Catalog Explorer permissions manage grants interactively. B, C, and D are unrelated.

**Question 35: A**  
A is correct because it grants schema usage. B, C, and D are invalid.

**Question 36: A**  
A is correct because it grants catalog usage. B, C, and D are invalid.

**Question 37: A**  
A is correct because `MODIFY` allows data changes on tables when other access requirements are met. B only helps discovery. C denies read in non-UC contexts. D is optimization.

**Question 38: A**  
A is correct because `BROWSE` supports discovery and access requests. B writes data. C and D are unrelated.

**Question 39: A**  
A is correct because external locations map cloud URIs to credentials. B, C, and D are unrelated.

**Question 40: A**  
A is correct because storage credentials abstract cloud storage credentials. B, C, and D are unrelated.

**Question 41: A**  
A is correct because managed tables get the most UC-managed lifecycle and optimization benefits. B and C are too broad. D is not persistent.

**Question 42: A**  
A is correct because external tables separate governance from file lifecycle. B, C, and D are false.

**Question 43: A**  
A is correct because managed tables are UC-governed and lifecycle-managed. B, C, and D are false.

**Question 44: A**  
A is correct because row filters hide rows. B masks values. C and D are unrelated.

**Question 45: A**  
A is correct because column masks redact values while preserving rows. B hides rows. C and D are triggers.

**Question 46: A**  
A is correct because views can encapsulate read-only SQL logic. B, C, and D are unrelated.

**Question 47: A**  
A is correct because ABAC centralizes policies through governed tags. B is false; grants are still needed. C and D are unrelated.

**Question 48: A**  
A is correct because read access typically requires parent usage and table SELECT. B grants too much. C denies access. D is insufficient.

**Question 49: A**  
A is correct because base grants plus ABAC tag-based masks meet both access and redaction requirements. B is over-permissive. C destroys data utility. D is unrelated.

**Question 50: A**  
A is correct because external tables use externally managed storage governed through external locations and storage credentials. B gives UC storage lifecycle control. C is compute only. D does not register data.

