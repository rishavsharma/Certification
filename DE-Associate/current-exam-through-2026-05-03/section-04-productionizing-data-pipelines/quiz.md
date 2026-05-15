# Section 4 Quiz (Current Exam Through May 3, 2026)

Exactly 50 exam-style multiple-choice questions. Each question has 4 options (A-D) with one best answer.

1. A team currently creates production jobs by manually editing tasks in the Jobs UI. They want a method that defines jobs as code, supports multi-environment targets, and standardizes validate/deploy/run steps. Which option best matches this goal?
A. Databricks SQL dashboards
B. Declarative Automation Bundles (DAB) / Asset Bundles
C. Editing notebook schedules directly in notebooks
D. Using an existing all-purpose cluster for every task

2. In a bundle project, which file must exist at the root of the bundle project folder (and only one of it is allowed)?
A. `bundle.yml`
B. `jobs.yml`
C. `databricks.yml`
D. `resources.yml`

3. Your bundle has resource YAML files in `resources/`, but `databricks bundle validate` reports that no resources are found. What is the most likely cause?
A. The bundle name is missing
B. The resource YAML files were not referenced by `include`
C. The resources folder must be named `assets/`
D. Targets cannot be used with resource files

4. In Databricks bundle terminology, where are Databricks objects like jobs and pipelines typically declared?
A. In the `resources` mapping
B. In the `sync` mapping only
C. In the `bundle` mapping only
D. Only in the workspace UI, never in YAML

5. A bundle defines targets `dev` and `prod`, and `dev` is marked as the default target. What happens if you run `databricks bundle deploy` without specifying a target?
A. It always deploys to `prod`
B. It fails because a target is required
C. It deploys to the default target (`dev`)
D. It deploys to both `dev` and `prod`

6. Which CLI option is used to deploy or run a bundle against a specific configured target?
A. `--workspace`
B. `--target` (or `-t`)
C. `--env`
D. `--cloud`

7. Which statement best describes what `databricks bundle plan` is used for?
A. It runs the job immediately
B. It previews the deployment actions without making changes
C. It deletes the deployed resources
D. It installs Python dependencies on the cluster

8. Which command is primarily responsible for creating or updating the bundle-managed resources in the remote workspace?
A. `databricks bundle deploy`
B. `databricks bundle validate`
C. `databricks bundle run`
D. `databricks bundle sync`

9. Which command is primarily responsible for triggering execution of a job or pipeline defined in a bundle after it has been deployed?
A. `databricks bundle deploy`
B. `databricks bundle validate`
C. `databricks bundle run`
D. `databricks bundle plan`

10. A bundle deploy previously created a job in the target workspace. The job definition is later removed from the bundle configuration and the bundle is deployed again to the same target. What is the most likely outcome?
A. The old job remains forever because bundles never delete resources
B. The old job is removed from the target workspace if it was previously deployed by that bundle
C. The bundle deploy fails until you manually delete the job
D. The job is renamed automatically to avoid conflicts

11. Two different bundle projects are accidentally configured with the same effective identity in the same workspace and target. What risk is most likely?
A. Nothing; bundles always namespace resources by job name only
B. The deployments can interfere, updating or deleting each other's managed resources
C. They will automatically merge into one bundle project
D. Both bundles will be blocked because bundles cannot be deployed to the same workspace

12. In the bundle CLI documentation, what setting is described as determining a bundle's unique identity (with a default like `~/.bundle/${bundle.name}/${bundle.target}`)?
A. `root_path`
B. `job_name`
C. `cluster_id`
D. `run_id`

13. A team wants to avoid "click-ops" and instead manage clusters and jobs using infrastructure-as-code across environments. Which "traditional deployment method" is explicitly supported by Databricks for this?
A. GitHub Actions only
B. The Databricks Terraform provider
C. Copying notebooks to DBFS manually
D. Exporting Spark UI event logs

14. A developer runs `databricks bundle` commands from inside the bundle folder. According to the bundle command reference, which file's settings are used for authentication context by default?
A. `.env`
B. `databricks.yml`
C. `pyproject.toml`
D. `requirements.txt`

15. A bundle includes `resources/*.yml` and also wants to include shared configuration files from outside the bundle folder. Which bundle feature supports including additional paths?
A. `git subtree`
B. The `sync.paths` mapping
C. Job run repair history
D. Spark UI stages table

16. When migrating an existing job definition into a bundle, which sequence best matches the official guidance?
A. Copy the job YAML from the UI, paste into bundle config, then download referenced notebooks/code into the bundle and update paths to local locations
B. Export the Spark UI, then run `bundle plan`
C. Copy the job name only, then run `bundle deploy`
D. Convert the job to a SQL warehouse, then migrate

17. Which statement best distinguishes bundles from simply scheduling a notebook in the UI?
A. Bundles cannot deploy notebooks
B. Bundles package artifacts and resource definitions together, enabling repeatable multi-target promotion
C. Bundles run only in interactive compute
D. Bundles only work for SQL queries

18. Your bundle defines `targets.dev.default: true` and `targets.prod`. You want to validate and deploy only to prod. Which choice is best?
A. Run `databricks bundle deploy` twice
B. Use `-t prod` (or `--target prod`) on the bundle commands
C. Rename `prod` to `default`
D. Delete the dev target

19. A user says: "I deployed the bundle, so the job already ran." What is the best correction?
A. Correct; deploy always triggers a run
B. Incorrect; deployment creates/updates resources, and running is a separate step
C. It depends only on the bundle name
D. Only pipelines run automatically after deploy, not jobs

20. In the workspace UI deployment experience for bundles, which statement is most accurate?
A. Resources can be run from the bundle view even if they were never deployed
B. Bundles deployed from the workspace always deploy to a different workspace than the CLI
C. A resource typically must be deployed before it can be run from the bundle view
D. Workspace bundle deploy always duplicates assets for every deploy attempt

21. Source-linked deployments are discussed in the context of deploying bundles from the workspace UI. Which statement is correct?
A. Source-linked deployments apply only when deploying through the workspace UI, and the preset is ignored by the CLI `bundle deploy`
B. Source-linked deployments are mandatory for production targets
C. Source-linked deployments mean bundle deploy also runs all workflows automatically
D. Source-linked deployments disable targets

22. Which structure best matches the typical bundle template output for a job project?
A. `databricks.yml` under `resources/` and notebooks under `targets/`
B. `databricks.yml` at root, `resources/` for job YAML, and `src/` for notebooks/code
C. `bundle.json` at root, `pipelines/` for YAML, and `sql/` for notebooks
D. Only notebooks in the workspace; no local files

23. The exam objective says "Deploy a workflow, repair, and rerun a task in case of failure." In modern docs, which product area most directly maps to this?
A. Delta Sharing
B. Unity Catalog
C. Lakeflow Jobs (Jobs & Pipelines UI)
D. Lakeflow Connect

24. In Databricks job operations, what is a "repair run" used for?
A. To permanently delete a job
B. To re-run one or more tasks as part of an original job run for recovery
C. To upgrade the Databricks Runtime version used by the job
D. To validate a bundle configuration

25. Which condition must be true to repair a run using the Databricks CLI `jobs repair-run` command?
A. The run must be in progress
B. The run must not be in progress
C. The job must be a single-task job
D. The job must run only SQL tasks

26. Which statement about repair runs is correct according to the CLI documentation?
A. Tasks are re-run as part of the original job run and use the current job and task settings
B. Repair runs always create a brand-new job with a new job_id
C. Repair runs can only be triggered from the Spark UI
D. Repair runs require cluster creation permission in all cases

27. According to Databricks job failure repair guidance, repair is supported only for which type of job?
A. Jobs with exactly one task
B. Jobs that orchestrate two or more tasks
C. Jobs that run only on all-purpose clusters
D. Jobs that do not use notebooks

28. A multi-task job run failed in Task B. Task C depends on B and was skipped. You fix the underlying issue in Task B and want to re-run B and also re-run downstream work that depends on B. What is the best operational action?
A. Start a repair run that re-runs B and dependent tasks
B. Delete the job and recreate it
C. Change the bundle name and redeploy
D. Export the run results and re-import them

29. A job has tasks that share the same job cluster. What can happen when you start a repair run?
A. The repair run cannot use job clusters
B. A repair run can create a new versioned job cluster (for example `_v1`) with current settings
C. The job cluster is always reused without changes
D. The job cluster is converted to a SQL warehouse

30. A user expects a repair run to use the exact cluster configuration from the original run that failed months ago. What is the best correction?
A. Correct; repair runs always pin to historical settings
B. Incorrect; repair runs use the current job/task settings
C. Repair runs ignore task settings entirely
D. Repair runs only re-run skipped tasks

31. Which UI feature is highlighted by Databricks as helping you quickly identify which tasks failed and which were skipped due to dependencies?
A. The cluster event log
B. The matrix view of job runs for a multi-task job
C. The SQL editor history
D. The token management page

32. A job run failed and you need to find the failure quickly. Which is the best first step in the official repair guidance?
A. Immediately run `bundle destroy`
B. Use the Jobs UI to locate the failed task in the run history (runs view)
C. Open the Spark UI stages page for an unrelated cluster
D. Reinstall the Databricks CLI

33. Which statement best describes "deploying a workflow" when using bundles?
A. Running `bundle validate` is sufficient to deploy
B. `bundle deploy` creates/updates the job definition and uploads referenced artifacts into the workspace path used by the bundle
C. `bundle run` creates the job definition but does not upload artifacts
D. Bundles cannot deploy jobs

34. A team wants to manage jobs programmatically but does not want to write raw REST calls. Which is an officially supported approach?
A. Databricks SDKs (for example Python SDK)
B. Spark UI export
C. Running only ad hoc notebooks
D. Using `dbutils` to create jobs

35. Which action is most appropriate if you want to execute the entire workflow again end-to-end with new parameters, not just recover from a failure?
A. Repair run
B. Start a new run
C. Rename the job cluster to `_v1`
D. Reduce shuffle partitions in Spark UI

36. Which statement about rerunning tasks in repair runs is most consistent with Databricks CLI options?
A. Repair runs can rerun failed tasks and can optionally rerun dependent tasks
B. Repair runs can only rerun successful tasks
C. Repair runs rerun all tasks unconditionally
D. Repair runs always skip dependent tasks

37. An engineer says: "Repair runs will change the job_id, so alerts tied to the job will break." What is the best correction?
A. Correct; job_id always changes on repair
B. Incorrect; repair runs are part of the original run history for that job
C. Correct; repair creates a new job definition for every attempt
D. Repair can only be used from Terraform

38. Which view in the Jobs UI is described as showing run progress split into individual tasks, useful for understanding which task took time?
A. Timeline
B. Catalog Explorer
C. Compute page
D. Secret scopes

39. Which statement is true about job creation and management tools according to official docs?
A. Jobs can only be created from the UI
B. Jobs can be created and managed via UI, CLI, and REST API
C. Jobs can only be created via Terraform
D. Jobs cannot be managed as code

40. A bundle-based job is deployed. Where would you typically confirm that the job resource was created?
A. In the SQL warehouse details page
B. In Jobs & Pipelines (Jobs filter)
C. In the Unity Catalog lineage graph
D. In the token list

41. Which statement best describes the exam objective "Use serverless for a hands-off, auto-optimized compute managed by Databricks" in the context of workflows?
A. Always use an all-purpose cluster so you can edit it during the run
B. Use serverless compute for workflows so Databricks manages provisioning, scaling, and optimization
C. Use instance pools to speed up classic cluster start times
D. Use only single-node clusters for production

42. Which requirement is explicitly listed for serverless compute for workflows?
A. Unity Catalog must be enabled in the workspace
B. The workspace must be in single-user access mode
C. Cluster creation permission is required
D. Only AWS workspaces are supported

43. Which access mode requirement is explicitly called out for serverless compute for workflows?
A. Legacy access mode
B. Standard access mode
C. Single-user access mode
D. No-isolation shared access mode

44. Which is a documented limitation of serverless compute for notebooks and jobs?
A. It supports compute-scoped init scripts
B. It supports environment variables directly
C. It does not support compute-scoped libraries and recommends notebook-scoped libraries instead
D. It requires instance pools

45. You updated a custom Python package used by a serverless job, but the job still runs the old behavior. According to serverless environment docs, what is the most likely fix?
A. Clear the Spark UI cache
B. Update the package version so the job picks up the latest implementation
C. Switch to a SQL warehouse
D. Convert the package to a notebook

46. You need to use the Spark UI to optimize a query. According to the official Spark UI guide, what is the recommended first investigative step?
A. Start at the Executors tab and sort by disk usage
B. Identify the longest stage of the job and investigate it
C. Only look at driver logs
D. Ignore stages and focus on cluster tags

47. On the Spark UI stage list, which columns are emphasized by Databricks for quickly understanding what the stage did at a high level?
A. GC Time and Scheduler Delay only
B. Input, Output, Shuffle Read, Shuffle Write
C. Number of notebooks and number of users
D. Token count and JVM version

48. A stage is long-running and shows spill. What does spill most directly indicate?
A. The stage wrote too much output to Delta
B. Spark ran low on memory and moved data from memory to disk (often during shuffle)
C. The job is running on Photon
D. The stage had no shuffle

49. What is skew in the context of Spark UI diagnosis?
A. When one or a few tasks take much longer than the rest, causing poor utilization and longer runtime
B. When all tasks take exactly the same time
C. When the job uses a SQL warehouse
D. When the driver JVM is restarted

50. A stage shows that max task duration is much higher than the 75th percentile duration. Based on the Spark UI guide, what is the best interpretation?
A. Healthy distribution; nothing to investigate
B. Potential skew; investigate why a few tasks are much slower
C. Guaranteed spill; increase cluster size
D. Guaranteed I/O bound; add more partitions

