# Section 4 (Current Exam Through May 3, 2026): DABs and Asset Bundles

## Scope / Objectives (keep current-exam wording visible)

This guide covers these **Section 4: Productionizing Data Pipelines** objectives from the exam guide version live **through May 3, 2026**:

- **Identify the difference between DAB and traditional deployment methods.**
- **Identify the structure of Asset Bundles.**

## Terminology Drift Note (read this before studying)

Databricks terminology has shifted in product docs. For the **current exam through May 3, 2026**, keep the exam wording, but normalize as follows while you study:

- Exam says **DAB** and **Asset Bundles**. In Databricks docs, you will see **Declarative Automation Bundles** (newer) and references to **Databricks Asset Bundles** (older).
- Exam says **workflow**. In Databricks docs, orchestration is now commonly described as **Lakeflow Jobs** (Jobs & Pipelines UI).

Practical interpretation for this section:

- Treat **DAB / Asset Bundles / Databricks Asset Bundles / Declarative Automation Bundles** as the same "bundle" concept for exam purposes.

## Core Concepts

### What a bundle is (and what it is not)

A bundle is a **project-as-code** packaging and deployment mechanism:

- You describe Databricks resources (for example, **jobs** and **pipelines**) as configuration files.
- You deploy the project to a workspace target (dev, staging, prod) and run the defined workflows after deployment.

A bundle is not:

- A runtime system by itself (it does not replace Jobs).
- "Just a notebook export" (it includes metadata, targets, and resource definitions).

Official overview: "What are Declarative Automation Bundles?" explains that bundles describe resources as source and support validation, deployment, and running workflows.  
Source: https://docs.databricks.com/aws/en/dev-tools/bundles/

### Why bundles are different from "traditional deployment methods"

**Traditional deployment approaches** you will see in practice:

- Click-ops in the Jobs UI (create/edit jobs manually).
- Scripting against the REST API / SDK / CLI to create or update jobs.
- Infrastructure-as-code (for example, the Databricks Terraform provider) to manage workspaces and resources.

**What bundles add** (high-yield exam differences):

- **A single, opinionated bundle root** that defines project identity and how artifacts + resources get deployed.
- **Targets** (dev/prod) in the same configuration with environment-specific overrides.
- **Validation, planning, deployment, run** workflows as standard CLI actions (`databricks bundle validate/plan/deploy/run`).
- A deployment mechanism that tracks what it created and can remove resources that were previously deployed.

Official job configuration docs explicitly call out that jobs can be managed via UI, CLI, REST API, and (older wording) Asset Bundles.  
Source: https://docs.databricks.com/aws/en/jobs/configure-job

Official Terraform docs describe Terraform as an automation option to deploy/manage Databricks resources (clusters, jobs, etc.).  
Source: https://docs.databricks.com/aws/en/dev-tools/terraform/

### Bundle identity and state (why "where you deploy" matters)

High-yield behavior for exam-style questions:

- Bundles have a **unique identity** in a workspace, driven by configuration such as `root_path` and target selection.
- Bundle deployments track resources they create using **IDs** and store state in the workspace file system.
- If you deploy multiple bundles with overlapping identity settings, they can interfere with each other.

Official source: Databricks CLI `bundle` command reference (deployment identity and `root_path`).  
Source: https://docs.databricks.com/aws/en/dev-tools/cli/bundle-commands

### The required `databricks.yml` file and "bundle root"

High-yield structure rule:

- A bundle must contain **exactly one** `databricks.yml` at the **root** of the bundle project folder.
- `databricks.yml` can reference other config files via `include`.

Official source: "Declarative Automation Bundles configuration".  
Source: https://docs.databricks.com/aws/en/dev-tools/bundles/settings

### Typical bundle folder structure (mental model)

Many official templates and tutorials follow a pattern like:

- `databricks.yml` at the root
- `resources/` containing YAML resource definitions (jobs, pipelines, etc.)
- `src/` containing notebooks and code artifacts referenced by jobs/pipelines
- Optional `tests/` for unit/integration tests

Official job bundle tutorial lists these generated files and directories explicitly.  
Source: https://docs.databricks.com/aws/en/dev-tools/bundles/jobs-tutorial

### Resources, targets, includes, and variables (the "exam core")

The parts you should be able to reason about quickly:

- `include`: which YAML files become part of the bundle configuration.
- `resources`: what Databricks objects the bundle manages (jobs, pipelines, etc.).
- `targets`: which environment you deploy to, and what overrides apply.
- `variables`: parameterize environment-specific settings (catalog/schema, cluster IDs, warehouse IDs, etc.).

Official source for full configuration shape: "Configuration reference".  
Source: https://docs.databricks.com/gcp/en/dev-tools/bundles/reference

Official resource reference (including job resource): "Declarative Automation Bundles resources".  
Source: https://docs.databricks.com/gcp/en/dev-tools/bundles/resources

## Examples and Decision Tables

### Decision table: DAB/bundles vs other deployment methods

Use this to answer "which approach is best" questions.

| You need... | Best fit | Why |
| --- | --- | --- |
| Repeatable multi-env deploy of jobs/pipelines with a single project definition | Bundles (DAB / Asset Bundles) | Targets, variable overrides, and standardized validate/deploy/run lifecycle |
| Quick one-off job changes in a single workspace | Jobs UI | Fastest when you do not need consistent promotion to prod |
| Full IaC coverage across accounts/workspaces and broader infra | Terraform provider | Workspace + infra automation, broader surface area |
| A custom app to manage jobs/runs programmatically | REST API / SDK | Fine-grained programmatic control |

### Example: Minimal "shape" of a bundle configuration (annotated)

This is not a complete reference, but it captures what the exam expects you to recognize.

```yaml
# databricks.yml (must be at the bundle root)
bundle:
  name: my_project

# Pull in resource config files (common pattern: resources/*.yml)
include:
  - resources/*.yml

# Define deployment targets (dev/prod) and workspace locations
targets:
  dev:
    default: true
    mode: development
    workspace:
      host: https://<dev-workspace-host>
  prod:
    mode: production
    workspace:
      host: https://<prod-workspace-host>
```

Official configuration rule: one bundle root `databricks.yml` and `include` referencing resource files.  
Source: https://docs.databricks.com/aws/en/dev-tools/bundles/settings

### Example: Bundle lifecycle commands you should recognize

From the Databricks CLI, the bundle command group supports:

- `databricks bundle validate` (catch config errors before deployment)
- `databricks bundle plan` (preview actions)
- `databricks bundle deploy` (create/update managed resources in the target workspace)
- `databricks bundle run` (trigger a job/pipeline defined in the bundle)
- `databricks bundle destroy` (remove resources/files deployed for that target)

Official CLI reference: `bundle` command group.  
Source: https://docs.databricks.com/aws/en/dev-tools/cli/bundle-commands

### Example: Deploying and running from within the workspace (UI-based bundles)

Databricks also supports deploying a bundle from within the workspace UI (useful in troubleshooting/collaboration scenarios). Key exam takeaways:

- A resource generally must be **deployed** before it can be run from the workspace bundle view.
- "Source-linked deployments" can apply when deploying from the workspace to development targets, and that behavior is **not** the same as deploying via CLI.

Official source: "Deploy bundles and run workflows from the workspace".  
Source: https://docs.databricks.com/aws/en/dev-tools/bundles/workspace-deploy

## Common Exam Traps (Section 4)

- Confusing **DAB** as "Databricks Asset Bundles" vs "Declarative Automation Bundles". The docs renamed it; the exam objectives still test the same concept.
- Assuming `databricks.yml` can live anywhere. For bundles, it must be at the **bundle root** and there must be **one and only one**.
- Forgetting that additional YAML files usually must be pulled in via `include`. If you define resource files but do not include them, they are not part of the bundle config.
- Assuming `bundle deploy` also runs the workflow. Deployment creates/updates the resources; execution is a separate "run".
- Missing that bundle deploy identity matters (`root_path` and target); collisions can cause "unexpected updates".
- Treating Terraform as the same as bundles. Both are IaC-like, but bundles are purpose-built for Databricks workflows and project structure, while Terraform is broader infra/resource management.

## Official-Source Links (Databricks)

- Current exam guide PDF (covers the exam live through May 3, 2026 and the new version starting May 4, 2026): https://www.databricks.com/sites/default/files/2026-03/databricks-certified-data-engineer-associate-exam-guide-may-4-2026.pdf
- What are Declarative Automation Bundles?: https://docs.databricks.com/aws/en/dev-tools/bundles/
- Bundle configuration (`databricks.yml`, `include`, targets, variables): https://docs.databricks.com/aws/en/dev-tools/bundles/settings
- Bundle configuration reference: https://docs.databricks.com/gcp/en/dev-tools/bundles/reference
- Bundle resources reference (job resource, pipeline resource, etc.): https://docs.databricks.com/gcp/en/dev-tools/bundles/resources
- Develop a job with bundles tutorial: https://docs.databricks.com/en/jobs/how-to/use-bundles-with-jobs.html
- `bundle` command group (CLI): https://docs.databricks.com/aws/en/dev-tools/cli/bundle-commands
- Deploy bundles from the workspace UI: https://docs.databricks.com/aws/en/dev-tools/bundles/workspace-deploy
- Databricks Terraform provider (example of "traditional" IaC method): https://docs.databricks.com/aws/en/dev-tools/terraform/

