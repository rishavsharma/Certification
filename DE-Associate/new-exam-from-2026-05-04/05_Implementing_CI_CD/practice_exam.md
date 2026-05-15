# Section 5: Implementing CI/CD - Practice Exam

Exam guide version: NEW EXAM GUIDE, valid on or after 4-May-2026.

Question style: multiple-choice with four plausible options, modeled after the sample questions in the attached guide. These are original practice questions, not official exam questions.

## Questions

**Question 1**  
**Objective:** Manage code workflow in Databricks Repos/Git folders  
A data engineer wants to isolate notebook changes for a new feature before merging them into main. What should they do first in the Git folder UI?

A. Create a new branch.  
B. Delete the remote repository.  
C. Deploy directly to prod.  
D. Rename the SQL warehouse.

**Question 2**  
**Objective:** Manage code workflow in Databricks Repos/Git folders  
A developer edited a notebook in a Databricks Git folder and wants the changes available in the remote Git provider. Which action is required?

A. Commit and push the changes.  
B. Run `OPTIMIZE` on the notebook.  
C. Grant SELECT on the notebook.  
D. Create a table update trigger.

**Question 3**  
**Objective:** Manage code workflow in Databricks Repos/Git folders  
A team member made changes in the remote repository. What should a Databricks user do to update their Git folder with those changes?

A. Pull changes from the remote repository.  
B. Create a SQL dashboard.  
C. Run a Lakeflow file arrival trigger.  
D. Revoke all catalog permissions.

**Question 4**  
**Objective:** Manage code workflow and pull requests  
A developer does not have permission to commit directly to the default branch. What is the recommended collaboration workflow?

A. Push a feature branch and create a pull request in the Git provider.  
B. Edit production notebooks directly with no review.  
C. Store changes only in notebook output.  
D. Use `COPY INTO` to merge code.

**Question 5**  
**Objective:** Manage code workflow in Databricks Repos/Git folders  
What is a primary purpose of Databricks Git folders?

A. Integrate Git repositories into the workspace for code development and collaboration.  
B. Replace Unity Catalog as the governance layer.  
C. Run Spark tasks without compute.  
D. Store DBU pricing tables only.

**Question 6**  
**Objective:** Manage code workflow in Databricks Repos/Git folders  
A data engineer switches branches in a shared Git folder. What should they understand?

A. The visible files can change to match the selected branch.  
B. The branch switch automatically deploys production jobs.  
C. All Unity Catalog permissions are revoked.  
D. Delta transaction history is deleted.

**Question 7**  
**Objective:** Manage code workflow in Databricks Repos/Git folders  
Which asset type is commonly version-controlled in Databricks Git folders?

A. Notebook source files.  
B. Running cluster memory.  
C. A Spark stage.  
D. A DBU invoice.

**Question 8**  
**Objective:** Manage code workflow in Databricks Repos/Git folders  
What is the main difference between commit and push?

A. Commit records local Git changes; push sends committed changes to the remote repository.  
B. Commit deploys to prod; push deletes the branch.  
C. Commit grants Unity Catalog privileges; push revokes them.  
D. Commit runs a SQL warehouse; push stops it.

**Question 9**  
**Objective:** Manage code workflow in Databricks Repos/Git folders  
A developer wants to review changed notebook source before committing. Which Databricks Git feature helps?

A. Git dialog diff/review.  
B. Spark UI storage tab.  
C. Predictive optimization history.  
D. Table update trigger page.

**Question 10**  
**Objective:** Understand bundle configuration  
What file is the root configuration file for a Declarative Automation Bundle?

A. `databricks.yml`  
B. `requirements.lock.sql`  
C. `unity_catalog.jsonl`  
D. `spark_history.yaml`

**Question 11**  
**Objective:** Understand bundle targets  
A single codebase must deploy to dev, test, and prod with different catalogs. Which bundle feature supports this?

A. Targets with variable overrides.  
B. Local notebook output.  
C. Cross joins.  
D. Manual SQL comments.

**Question 12**  
**Objective:** Understand environment-specific configuration  
A bundle variable `catalog` should be `de_dev` in dev and `de_prod` in prod. Where should these values be configured?

A. Under each target's `variables` override.  
B. In the Delta transaction log manually.  
C. In a dashboard title.  
D. In the Spark UI stage metrics page.

**Question 13**  
**Objective:** Understand bundle variables  
Which syntax references a custom bundle variable named `catalog`?

A. `${var.catalog}`  
B. `${catalog.var}`  
C. `{{catalog-only}}`  
D. `GRANT catalog`

**Question 14**  
**Objective:** Understand bundle substitutions  
Which substitution commonly identifies the selected deployment target?

A. `${bundle.target}`  
B. `${spark.stage}`  
C. `${delta.version}`  
D. `${sql.row}`

**Question 15**  
**Objective:** Deploy bundles across environments  
A job name should differ between dev and prod to avoid collisions. Which name pattern is appropriate?

A. `orders_etl_${bundle.target}`  
B. `orders_etl_hardcoded_prod` in every target  
C. `orders_etl` with no target separation anywhere  
D. `orders_etl_${spark.driver.memory}`

**Question 16**  
**Objective:** Deploy Lakeflow Jobs with bundles  
Which bundle resource type defines a Lakeflow Job?

A. `resources.jobs`  
B. `resources.catalog_permissions_only`  
C. `resources.spark_ui_stages`  
D. `resources.local_downloads`

**Question 17**  
**Objective:** Deploy pipelines with bundles  
Which bundle resource type defines Lakeflow Spark Declarative Pipelines?

A. `resources.pipelines`  
B. `resources.sql_comments`  
C. `resources.git_branches`  
D. `resources.cost_exports`

**Question 18**  
**Objective:** Understand bundle lifecycle  
Which command checks bundle configuration before deployment?

A. `databricks bundle validate -t dev`  
B. `databricks bundle ignore -t dev`  
C. `databricks jobs delete-all`  
D. `databricks sql deny bundle`

**Question 19**  
**Objective:** Deploy bundles using CLI  
Which command deploys a bundle to the prod target?

A. `databricks bundle deploy -t prod`  
B. `databricks bundle validate -t prod` only  
C. `databricks repos branch -t prod`  
D. `databricks tables optimize -t prod`

**Question 20**  
**Objective:** Manage bundle resources with CLI  
Which command runs a deployed bundle job named `orders_pipeline` in the dev target?

A. `databricks bundle run -t dev orders_pipeline`  
B. `databricks bundle deploy -t dev orders_pipeline` only  
C. `databricks git pull orders_pipeline`  
D. `databricks clusters repair orders_pipeline`

**Question 21**  
**Objective:** Understand bundle lifecycle  
In a CI pipeline, why should `bundle validate` run before `bundle deploy`?

A. It catches many schema and configuration errors before changing the workspace.  
B. It deletes all production resources.  
C. It replaces Git pull requests.  
D. It grants users table access.

**Question 22**  
**Objective:** Manage CI/CD workflow  
A pull request is merged to main after tests pass. What should the deployment pipeline use to promote the same codebase?

A. Bundle deploy commands with the appropriate target.  
B. Manual copy-paste into prod notebooks.  
C. A local file upload into Catalog Explorer.  
D. A cross join of dev and prod tables.

**Question 23**  
**Objective:** Understand Git folders vs bundles  
Which statement is correct?

A. Git folders support interactive source control; bundles support repeatable deployment as code.  
B. Git folders replace all CI/CD commands.  
C. Bundles are only for dashboard screenshots.  
D. Git folders are a replacement for Delta tables.

**Question 24**  
**Objective:** Understand environment-specific configuration  
A notebook hard-codes `de_dev.silver.orders` and is deployed to prod. What is the risk?

A. The prod job may read or write dev data; use variables or parameters instead.  
B. The notebook automatically rewrites the catalog to prod.  
C. The job cannot run in dev.  
D. Git automatically blocks all hard-coded strings.

**Question 25**  
**Objective:** Understand bundle variables and overrides  
A SQL task needs a different SQL warehouse ID in dev and prod. What should be parameterized?

A. `warehouse_id` using a bundle variable per target.  
B. The notebook output display setting.  
C. The Delta table protocol manually.  
D. The branch name only.

**Question 26**  
**Objective:** Deploy workspace assets with bundles  
Which assets can be included in bundle definitions?

A. Jobs, pipelines, notebooks/files, dashboards, and other supported Databricks resources.  
B. Only local laptop screenshots.  
C. Only DBU invoices.  
D. Only Spark UI task metrics.

**Question 27**  
**Objective:** Understand bundle project structure  
Why might a bundle use `include: - resources/*.yml`?

A. To split resource definitions into separate YAML files while keeping one bundle.  
B. To import rows from CSV into a Delta table.  
C. To grant table privileges automatically without configuration.  
D. To hide all resources from validation.

**Question 28**  
**Objective:** Manage code workflow  
A feature branch has uncommitted changes. The developer switches branches. What can happen?

A. Uncommitted changes may carry over if they do not conflict, so the developer should manage them carefully.  
B. All changes are guaranteed to be deployed to prod.  
C. Unity Catalog deletes the metastore.  
D. The SQL warehouse restarts automatically.

**Question 29**  
**Objective:** Manage code workflow  
Where is a pull request usually created?

A. In the Git provider such as GitHub, GitLab, Azure DevOps, or Bitbucket.  
B. In the Spark executor log.  
C. In the Delta transaction log.  
D. In a SQL warehouse size selector.

**Question 30**  
**Objective:** Understand CLI in CI/CD  
A CI runner needs to deploy Databricks resources automatically. Which tool is most directly involved?

A. Databricks CLI.  
B. Catalog Explorer only.  
C. Manual notebook output.  
D. Local browser bookmarks.

**Question 31**  
**Objective:** Understand CI/CD identity  
For automated production deployments, which identity pattern is generally preferable?

A. A service principal or workload identity configured for CI/CD.  
B. A personal developer token pasted in source code.  
C. A hard-coded password in notebook markdown.  
D. An anonymous user.

**Question 32**  
**Objective:** Understand Git credentials  
Why must Databricks Git integration be configured with Git credentials?

A. To authenticate Git operations such as clone, pull, commit, and push.  
B. To create Delta transaction logs.  
C. To tune shuffle partitions.  
D. To refresh dashboards.

**Question 33**  
**Objective:** Understand bundle deploy behavior  
What does `databricks bundle deploy -t test` do conceptually?

A. Creates or updates the bundle's configured resources in the test target workspace/settings.  
B. Creates a pull request.  
C. Drops every table in test.  
D. Runs only local unit tests and never contacts Databricks.

**Question 34**  
**Objective:** Understand bundle run behavior  
What does `databricks bundle run -t dev orders_job` do conceptually?

A. Runs a deployed bundle resource named `orders_job` using the dev target.  
B. Validates YAML only and never runs a job.  
C. Deletes the dev target.  
D. Creates a Git branch.

**Question 35**  
**Objective:** Understand bundle destroy behavior  
When might `databricks bundle destroy -t dev` be appropriate?

A. To remove deployed dev resources created by the bundle when they are no longer needed.  
B. To merge a pull request.  
C. To refresh a production dashboard.  
D. To change a Spark shuffle setting.

**Question 36**  
**Objective:** Understand bundle configuration  
What is the purpose of the `bundle.name` setting?

A. It identifies the bundle project.  
B. It sets the number of Spark executors.  
C. It grants table SELECT.  
D. It enables row filters.

**Question 37**  
**Objective:** Understand resource dependencies in bundles  
A bundle job task should run after another task. Which field is relevant in the job resource YAML?

A. `depends_on`  
B. `git_push_only`  
C. `delta_history`  
D. `deny_privilege`

**Question 38**  
**Objective:** Understand Databricks Repos/Git folders  
Which operation retrieves remote changes into the Databricks Git folder?

A. Pull  
B. Push  
C. OPTIMIZE  
D. VACUUM

**Question 39**  
**Objective:** Understand Databricks Repos/Git folders  
Which operation sends committed local branch changes to the remote repository?

A. Push  
B. Pull  
C. Table update trigger  
D. SQL refresh

**Question 40**  
**Objective:** Understand CI/CD promotion  
A team maintains separate copied notebooks for dev, test, and prod. What is a better CI/CD pattern?

A. One codebase with environment-specific bundle targets and variables.  
B. Three unrelated codebases edited manually.  
C. Prod-only notebooks with no testing.  
D. Local CSV uploads for code deployment.

**Question 41**  
**Objective:** Understand bundle validation  
A bundle references a variable that is not defined. Which command is likely to catch this before deploy?

A. `databricks bundle validate`  
B. `databricks jobs run-now` only  
C. `OPTIMIZE`  
D. `SHOW TABLES`

**Question 42**  
**Objective:** Deploy Lakeflow Jobs with bundles  
A bundle should deploy a job with a notebook task and a SQL task. Where should these be declared?

A. In a job resource under `resources.jobs`.  
B. In the Git provider pull request title only.  
C. In a Spark UI stage.  
D. In a table comment.

**Question 43**  
**Objective:** Deploy Lakeflow Spark Declarative Pipelines with bundles  
A bundle deploys a pipeline to catalog `${var.catalog}`. What does changing the target from dev to prod do if each target overrides `catalog`?

A. The same pipeline definition can deploy to the prod catalog value.  
B. It rewrites source code manually.  
C. It disables Unity Catalog.  
D. It deletes the dev branch.

**Question 44**  
**Objective:** Understand Git folders limitations  
Why should teams avoid using a shared Git folder branch switch as the main production deployment process?

A. Branch switching is an interactive development operation, while bundles provide controlled deployment.  
B. Branch switching always validates every resource.  
C. Branch switching creates all Unity Catalog grants.  
D. Branch switching is the only supported CI/CD mechanism.

**Question 45**  
**Objective:** Understand source control  
Why are pull requests useful in the Databricks development workflow?

A. They enable review and approval before merging changes into a shared branch.  
B. They compact Delta files.  
C. They configure serverless compute automatically.  
D. They replace all tests.

**Question 46**  
**Objective:** Understand CI/CD automation  
A GitHub Actions workflow should validate and deploy a bundle after merge. Which command sequence is most sensible?

A. `databricks bundle validate -t prod`, then `databricks bundle deploy -t prod`.  
B. `databricks bundle destroy -t prod`, then manual notebook edit.  
C. `COPY INTO prod FROM git`.  
D. `GRANT ALL PRIVILEGES ON BUNDLE`.

**Question 47**  
**Objective:** Understand environment-specific configuration  
A bundle uses `${workspace.current_user.userName}` to construct a dev workspace root path. What is this an example of?

A. A bundle substitution.  
B. A table update trigger.  
C. A Spark executor metric.  
D. A Delta merge condition.

**Question 48**  
**Objective:** Understand bundle variables  
Which statement about variables is correct?

A. Variables make configuration reusable and can be overridden per target.  
B. Variables are only stored in Delta table rows.  
C. Variables cannot be used in job definitions.  
D. Variables are the same as Spark task failures.

**Question 49**  
**Objective:** Understand Databricks CLI  
A developer runs `databricks bundle deploy` from outside the bundle root. Which environment variable can point the CLI to the bundle root?

A. `BUNDLE_ROOT`  
B. `SPARK_ROOT_TABLE`  
C. `DELTA_PUSH_ROOT`  
D. `UNITY_BRANCH`

**Question 50**  
**Objective:** Implementing CI/CD integrated scenario  
A team wants reviewed notebook changes, repeatable deployment of jobs and pipelines, dev/test/prod configuration, and automated validation. Which design is best?

A. Use Git folders for branch/commit/push and pull requests, then use Declarative Automation Bundles with targets, variables, `validate`, and `deploy` in CI/CD.  
B. Edit production notebooks directly and copy screenshots between workspaces.  
C. Use one hard-coded dev catalog and manually rename it in prod after deployment.  
D. Use `COPY INTO` to deploy notebooks into SQL warehouses.

## Answer Key

**Question 1: A**  
A is correct because feature branches isolate work. B deletes source control, C skips review, and D is unrelated.

**Question 2: A**  
A is correct because commit records the change and push sends it to the remote. B, C, and D do not update Git.

**Question 3: A**  
A is correct because pull retrieves remote changes. B, C, and D are unrelated.

**Question 4: A**  
A is correct because feature branch plus pull request supports review without direct default-branch commits. B, C, and D are unsafe or unrelated.

**Question 5: A**  
A is correct because Git folders integrate Git into the workspace. B, C, and D are unrelated.

**Question 6: A**  
A is correct because branch content controls visible files. B, C, and D are false.

**Question 7: A**  
A is correct because notebooks and files are version-controlled assets. B, C, and D are runtime or billing artifacts.

**Question 8: A**  
A is correct because commit and push are separate Git steps. B, C, and D are wrong.

**Question 9: A**  
A is correct because the Git dialog can show changes before commit. B, C, and D are unrelated.

**Question 10: A**  
A is correct because `databricks.yml` is the root bundle configuration file. B, C, and D are not bundle root files.

**Question 11: A**  
A is correct because targets and overrides support environment-specific deployment. B, C, and D do not.

**Question 12: A**  
A is correct because target variables override values per environment. B, C, and D are unrelated.

**Question 13: A**  
A is correct because `${var.catalog}` references a bundle variable. B, C, and D are invalid.

**Question 14: A**  
A is correct because `${bundle.target}` resolves to the active target. B, C, and D are unrelated.

**Question 15: A**  
A is correct because the target suffix prevents collisions. B and C hard-code or collide. D uses an unrelated setting.

**Question 16: A**  
A is correct because jobs are declared under `resources.jobs`. B, C, and D are invalid resource categories.

**Question 17: A**  
A is correct because pipelines are declared under `resources.pipelines`. B, C, and D are unrelated.

**Question 18: A**  
A is correct because `bundle validate` checks configuration. B, C, and D are invalid or unrelated.

**Question 19: A**  
A is correct because `deploy -t prod` deploys to prod. B validates only. C and D do not deploy bundles.

**Question 20: A**  
A is correct because `bundle run` runs a named resource. B deploys but does not run as stated. C and D are unrelated.

**Question 21: A**  
A is correct because validation catches errors before workspace changes. B, C, and D are false.

**Question 22: A**  
A is correct because bundles promote the same codebase by target. B is manual and error-prone. C and D are unrelated.

**Question 23: A**  
A is correct because Git folders and bundles serve different parts of CI/CD. B, C, and D are false.

**Question 24: A**  
A is correct because hard-coded dev references can pollute prod. B, C, and D are false.

**Question 25: A**  
A is correct because warehouse IDs differ by environment and should be variableized. B, C, and D do not solve environment config.

**Question 26: A**  
A is correct because bundles define supported Databricks resources and source files. B, C, and D are not bundle deployment targets.

**Question 27: A**  
A is correct because `include` modularizes YAML definitions. B, C, and D are false.

**Question 28: A**  
A is correct because uncommitted changes may carry over if not conflicting. B, C, and D are false.

**Question 29: A**  
A is correct because pull requests belong to the Git provider workflow. B, C, and D are unrelated.

**Question 30: A**  
A is correct because the Databricks CLI runs deployment commands in automation. B and C are manual. D is unrelated.

**Question 31: A**  
A is correct because automated CI/CD should use non-personal deployment identity. B and C expose secrets. D is invalid.

**Question 32: A**  
A is correct because credentials authenticate Git operations. B, C, and D are unrelated.

**Question 33: A**  
A is correct because deploy creates or updates resources for the target. B, C, and D are false.

**Question 34: A**  
A is correct because `bundle run` executes a deployed resource. B validates only. C and D are unrelated.

**Question 35: A**  
A is correct because destroy removes deployed resources for a target when appropriate. B, C, and D are unrelated.

**Question 36: A**  
A is correct because bundle name identifies the bundle. B, C, and D are unrelated settings.

**Question 37: A**  
A is correct because `depends_on` defines task ordering in job YAML. B, C, and D are invalid.

**Question 38: A**  
A is correct because pull retrieves remote changes. B sends changes out. C and D maintain Delta tables.

**Question 39: A**  
A is correct because push sends committed changes to remote. B retrieves changes. C and D are unrelated.

**Question 40: A**  
A is correct because one codebase with targets reduces drift. B, C, and D are unsafe or not CI/CD.

**Question 41: A**  
A is correct because validation catches undefined variables. B might fail later but is not validation. C and D are SQL/table commands.

**Question 42: A**  
A is correct because job task definitions belong in `resources.jobs`. B, C, and D do not define jobs.

**Question 43: A**  
A is correct because target overrides resolve variables differently per environment. B, C, and D are false.

**Question 44: A**  
A is correct because branch switching is not controlled deployment. B, C, and D are false.

**Question 45: A**  
A is correct because pull requests enable review. B, C, and D are unrelated or overclaims.

**Question 46: A**  
A is correct because validate then deploy is the sensible CI/CD sequence. B is destructive. C and D are invalid.

**Question 47: A**  
A is correct because it is a substitution resolved by bundle context. B, C, and D are unrelated.

**Question 48: A**  
A is correct because variables make bundle config reusable across targets. B, C, and D are false.

**Question 49: A**  
A is correct because `BUNDLE_ROOT` can point the CLI to the bundle root. B, C, and D are invalid.

**Question 50: A**  
A is correct because it combines source control, review, bundles, targets, validation, and deployment. B, C, and D are manual, hard-coded, or invalid patterns.

