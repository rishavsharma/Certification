# Section 4 Quiz Answers (Current Exam Through May 3, 2026)

Objective mapping used below (matches current exam wording):

- **OBJ 4.1**: Identify the difference between DAB and traditional deployment methods.
- **OBJ 4.2**: Identify the structure of Asset Bundles.
- **OBJ 4.3**: Deploy a workflow, repair, and rerun a task in case of failure.
- **OBJ 4.4**: Use serverless for a hands-off, auto-optimized compute managed by Databricks.
- **OBJ 4.5**: Analyzing the Spark UI to optimize the query.

1. **B** | OBJ 4.1 | Bundles (DAB/Asset Bundles) define workflows as code with targets and a validate/deploy/run lifecycle.
2. **C** | OBJ 4.2 | A bundle must contain exactly one `databricks.yml` at the bundle root.
3. **B** | OBJ 4.2 | Resource files must be pulled into the bundle configuration via `include`; otherwise they are not part of the bundle config.
4. **A** | OBJ 4.2 | Bundle-managed Databricks objects (jobs, pipelines, etc.) are declared under `resources`.
5. **C** | OBJ 4.2 | If a default target is configured, bundle commands use it when no target is specified.
6. **B** | OBJ 4.2 | Target selection is done with `--target` or `-t`.
7. **B** | OBJ 4.2 | `bundle plan` previews planned changes without deploying them.
8. **A** | OBJ 4.2 | `bundle deploy` is the action that creates/updates resources in the workspace for the selected target.
9. **C** | OBJ 4.2 | `bundle run` triggers execution of a resource (job/pipeline) defined in the bundle after deployment.
10. **B** | OBJ 4.2 | Bundle deployment tracks created resources; removing a resource from config can remove it on subsequent deploy if it was previously deployed by that bundle.
11. **B** | OBJ 4.1 | Same effective bundle identity can cause deployments to interfere (unexpected updates/deletes).
12. **A** | OBJ 4.2 | The CLI documentation describes `root_path` as central to bundle identity.
13. **B** | OBJ 4.1 | The Databricks Terraform provider is an official IaC option used to manage jobs/clusters/workspaces.
14. **B** | OBJ 4.2 | Bundle commands use `databricks.yml` settings for authentication when run in the bundle folder (per CLI docs).
15. **B** | OBJ 4.2 | `sync.paths` supports including and synchronizing additional directories (including shared code/config) into the workspace deployment.
16. **A** | OBJ 4.2 | Official migration guidance: copy YAML into bundle config, download referenced artifacts into the bundle (often `src/`), and update paths.
17. **B** | OBJ 4.1 | Bundles package artifacts plus resource definitions and enable repeatable promotion across targets.
18. **B** | OBJ 4.2 | Use `-t prod` to apply bundle commands against the `prod` target when `dev` is default.
19. **B** | OBJ 4.1 | Deploy updates resources; execution is a separate "run" step.
20. **C** | OBJ 4.2 | In the workspace bundle view, resources typically must be deployed before they can be run from that view.
21. **A** | OBJ 4.2 | Source-linked deployments apply to workspace UI bundle deployments; the preset is ignored by CLI deploy.
22. **B** | OBJ 4.2 | Typical bundle structure: root `databricks.yml`, `resources/` for YAML, `src/` for artifacts.

23. **C** | OBJ 4.3 | "Workflows" in the current exam map to Jobs orchestration; docs commonly call it Lakeflow Jobs.
24. **B** | OBJ 4.3 | A repair run reruns selected tasks as part of the original run for recovery.
25. **B** | OBJ 4.3 | A run must not be in progress to be repaired.
26. **A** | OBJ 4.3 | CLI docs state repaired tasks run within the original run and use current job/task settings.
27. **B** | OBJ 4.3 | Official repair guidance states repair is supported only for jobs with two or more tasks.
28. **A** | OBJ 4.3 | Repair run reruns failed tasks and (when selected) dependent downstream tasks that were skipped.
29. **B** | OBJ 4.3 | Repair runs can create a new versioned job cluster when tasks share a job cluster.
30. **B** | OBJ 4.3 | Repairs use current settings, not necessarily the exact historical settings from months ago.
31. **B** | OBJ 4.3 | The runs matrix view is highlighted as a fast way to identify failures and skipped tasks.
32. **B** | OBJ 4.3 | Official guidance starts with locating the failed task in the Jobs UI run history.
33. **B** | OBJ 4.3 | Bundle deploy updates job definitions and synchronizes artifacts; it is the "deployment" step.
34. **A** | OBJ 4.3 | Databricks SDKs are an official way to manage jobs without writing raw REST calls.
35. **B** | OBJ 4.3 | For an end-to-end re-execution with new parameters, you typically start a new run rather than repair.
36. **A** | OBJ 4.3 | CLI repair-run supports rerunning failed tasks and optionally rerunning dependent tasks.
37. **B** | OBJ 4.3 | Repair runs do not create a new job_id; they appear in the original job run history.
38. **A** | OBJ 4.3 | Timeline view shows run progress split by tasks and is used for operational analysis.
39. **B** | OBJ 4.3 | Official docs state jobs can be created/managed via UI, CLI, and REST API.
40. **B** | OBJ 4.3 | Jobs created by a bundle deploy are confirmed in Jobs & Pipelines (Jobs listing).

41. **B** | OBJ 4.4 | Serverless compute for workflows is Databricks-managed, auto-optimized compute for jobs.
42. **A** | OBJ 4.4 | Serverless compute for workflows requires Unity Catalog enabled (per docs).
43. **B** | OBJ 4.4 | Serverless compute for workflows uses standard access mode; workloads must support it.
44. **C** | OBJ 4.4 | Serverless limitations include no compute-scoped libraries; notebook-scoped libraries are recommended instead.
45. **B** | OBJ 4.4 | Serverless dependency docs call out versioning custom packages to pick up changes.

46. **B** | OBJ 4.5 | The official Spark UI guide starts by identifying the longest stage and investigating it.
47. **B** | OBJ 4.5 | Databricks emphasizes Input/Output and Shuffle Read/Write for stage-level diagnosis.
48. **B** | OBJ 4.5 | Spill indicates memory pressure where Spark moves data from memory to disk, commonly during shuffle.
49. **A** | OBJ 4.5 | Skew means a small number of tasks take far longer than others, hurting utilization and runtime.
50. **B** | OBJ 4.5 | A large gap between max and percentile task duration is a skew signal in the Spark UI guide.

