# Section 5: Implementing CI/CD - Learning Guide

Exam guide version: NEW EXAM GUIDE, valid on or after 4-May-2026.

## Section Objectives

This section covers four syllabus bullets from the NEW EXAM GUIDE:

1. Manage your code development workflow within the Databricks workspace UI, including creating and switching between branches in Databricks Repos, committing and pushing changes, and creating pull requests using Databricks Git integration.
2. Understand environment-specific configuration using Automation Bundle, formerly Databricks Asset Bundles, variables and overrides while promoting the same codebase across dev, test, and prod targets.
3. Deploy Declarative Automation Bundles, formerly Databricks Asset Bundles, to package, configure, and promote Lakeflow Jobs, Lakeflow Spark Declarative Pipelines, and other workspace assets across dev, test, and prod environments.
4. Understand the Databricks CLI to validate, deploy, and manage Declarative Automation Bundles and other workspace assets in automated CI/CD workflows.

## Naming Note

Databricks documentation increasingly uses "Databricks Git folders" for the feature previously called "Repos." The exam guide says "Databricks Repos." Treat exam scenarios about Repos, Git folders, Git integration, branches, commits, and pull requests as the same workspace Git workflow.

Databricks documentation also uses "Declarative Automation Bundles" for what was formerly called "Databricks Asset Bundles." The exam guide explicitly mentions both names.

## Exam Mental Model

CI/CD on Databricks has two layers:

1. Developer workflow: use Git folders/Repos for interactive development, branch switching, commits, pushes, and pull requests.
2. Deployment workflow: use Declarative Automation Bundles plus the Databricks CLI to validate and deploy jobs, pipelines, notebooks, dashboards, and supporting resources across dev, test, and prod.

Git folders manage code collaboration. Bundles manage repeatable deployment.

## Databricks Git Folders / Repos Workflow

### Common Daily Workflow

1. Clone a remote repository into a Databricks Git folder.
2. Create or switch to a feature branch.
3. Edit notebooks or files.
4. Review changed files in the Git dialog.
5. Commit and push changes to the remote repository.
6. Create a pull request in the Git provider.
7. Merge through the provider after review.
8. Pull the latest main branch into Databricks.

Exam focus:

- Branches isolate development work.
- Pull requests are created in the Git provider, not by changing a notebook cell.
- Commit and push sends workspace changes back to the remote repository.
- Pull retrieves remote changes.
- Notebook outputs are usually not committed by default in source formats.

### Branch Switching Caution

Switching branches changes the files visible in the Git folder. If assets exist on one branch but not another, switching branches can remove and recreate workspace assets.

Exam focus:

- Do not use branch switching as a production deployment mechanism.
- Use Git folders for development.
- Use Bundles for repeatable CI/CD deployment.

## Declarative Automation Bundles

A Declarative Automation Bundle is a project definition that packages source files and Databricks resource definitions. A bundle lets you deploy the same codebase to multiple targets such as dev, test, and prod.

Typical bundle contents:

```text
my_project/
  databricks.yml
  resources/
    jobs.yml
    pipelines.yml
  src/
    transformations/
      clean_orders.py
  notebooks/
    ingest_orders.py
```

The root file is `databricks.yml`. It defines the bundle name, workspace settings, includes, variables, resources, and deployment targets.

## Bundle Targets

Targets represent environments such as dev, test, and prod. A target can override variables, workspace paths, resource names, compute IDs, schedules, and permissions.

Example:

```yaml
bundle:
  name: de_associate_demo

include:
  - resources/*.yml

variables:
  catalog:
    description: Target catalog
    default: de_dev
  warehouse_id:
    description: SQL warehouse for SQL tasks

targets:
  dev:
    default: true
    variables:
      catalog: de_dev
      warehouse_id: 1234-dev

  test:
    variables:
      catalog: de_test
      warehouse_id: 1234-test

  prod:
    variables:
      catalog: de_prod
      warehouse_id: 1234-prod
```

Exam focus:

- Use targets to promote the same codebase across environments.
- Do not hard-code dev catalog names into production jobs.
- Variables and substitutions keep configuration reusable.

## Bundle Variables and Substitutions

Variables are custom values that can be overridden per target.

Example resource using a variable:

```yaml
resources:
  jobs:
    orders_etl:
      name: orders_etl_${bundle.target}
      tasks:
        - task_key: build_gold
          sql_task:
            query:
              query_id: abc123
            warehouse_id: ${var.warehouse_id}
```

Common substitutions:

- `${bundle.name}`
- `${bundle.target}`
- `${workspace.current_user.userName}`
- `${var.catalog}`
- `${resources.jobs.<job_key>.id}`

Exam focus:

- `${var.name}` references a variable.
- `${bundle.target}` changes based on target selection.
- `databricks bundle validate` catches many configuration issues before deployment.

## Bundle Resource Examples

### Lakeflow Jobs Resource

```yaml
resources:
  jobs:
    orders_pipeline:
      name: orders_pipeline_${bundle.target}
      tasks:
        - task_key: clean_orders
          notebook_task:
            notebook_path: ../notebooks/clean_orders.py
            base_parameters:
              catalog: ${var.catalog}
        - task_key: aggregate_daily_sales
          depends_on:
            - task_key: clean_orders
          sql_task:
            file:
              path: ../sql/aggregate_daily_sales.sql
            warehouse_id: ${var.warehouse_id}
```

### Lakeflow Spark Declarative Pipeline Resource

```yaml
resources:
  pipelines:
    silver_gold_pipeline:
      name: silver_gold_pipeline_${bundle.target}
      catalog: ${var.catalog}
      schema: silver
      serverless: true
      libraries:
        - notebook:
            path: ../pipelines/silver_gold.py
```

Exam focus:

- Bundles can define jobs and pipelines as code.
- Bundle resources are deployed to a target workspace.
- Target overrides allow different catalogs, schemas, paths, schedules, and compute per environment.

## Databricks CLI Bundle Commands

Validate configuration:

```bash
databricks bundle validate -t dev
```

Deploy to a target:

```bash
databricks bundle deploy -t dev
```

Run a bundle job:

```bash
databricks bundle run -t dev orders_pipeline
```

Deploy to production:

```bash
databricks bundle deploy -t prod
```

Destroy deployed resources for a target when appropriate:

```bash
databricks bundle destroy -t dev
```

Exam focus:

- Validate before deploy.
- `-t` selects a target.
- CI tools such as GitHub Actions, Azure DevOps, or GitLab CI can run these CLI commands.
- Use service principals/workload identity patterns for automated deployments rather than a personal interactive user where possible.

## CI/CD Promotion Pattern

Typical promotion flow:

1. Developer creates branch in Git folder.
2. Developer commits and pushes branch.
3. Pull request triggers CI.
4. CI runs tests and `databricks bundle validate -t dev` or `-t test`.
5. CI deploys to dev/test using `databricks bundle deploy -t test`.
6. Review and approval happen.
7. CD deploys the same bundle to prod using `databricks bundle deploy -t prod`.

Key idea:

The codebase is the same. Target configuration changes the environment.

## Common Exam Traps

- Confusing Git folders with deployment. Git folders are for source control; bundles are for deployment as code.
- Hard-coding production catalog names in notebooks instead of passing variables.
- Deploying without validating bundle configuration.
- Creating separate codebases for dev, test, and prod instead of one codebase with target overrides.
- Assuming pull requests are created by Databricks SQL. Pull requests belong to the Git provider.
- Forgetting to push commits after editing in a Databricks Git folder.
- Treating `databricks bundle run` as a replacement for `databricks bundle deploy`; run executes a deployed bundle resource, deploy creates or updates it.

## Mini Labs

### Lab 1: Git Folder Workflow

```text
Open Git folder -> Create branch feature/orders-quality
Edit notebook -> Review diff -> Commit & Push
Open Git provider -> Create pull request -> Merge after review
Pull latest main into Databricks
```

### Lab 2: Bundle Lifecycle

```bash
databricks bundle validate -t dev
databricks bundle deploy -t dev
databricks bundle run -t dev orders_pipeline
databricks bundle deploy -t prod
```

### Lab 3: Environment Variables

```yaml
variables:
  catalog:
    default: de_dev

targets:
  prod:
    variables:
      catalog: de_prod
```

In SQL or notebook code, use the passed catalog rather than hard-coding `de_dev`.

## Official References

- Databricks Certified Data Engineer Associate Exam Guide, May 4, 2026: https://www.databricks.com/sites/default/files/2026-03/databricks-certified-data-engineer-associate-exam-guide-may-4-2026.pdf
- Databricks Git folders: https://docs.databricks.com/aws/en/repos/
- Create and manage Git folders: https://docs.databricks.com/aws/en/repos/git-operations-with-repos
- Git folders concepts: https://docs.databricks.com/aws/en/repos/git-folders-concepts
- Configure Git integration: https://docs.databricks.com/aws/en/repos/repos-setup
- Supported asset types in Git folders: https://docs.databricks.com/aws/en/repos/supported-artifact-types
- What are Databricks Asset Bundles?: https://docs.databricks.com/aws/dev-tools/bundles/
- Develop Declarative Automation Bundles: https://docs.databricks.com/aws/en/dev-tools/bundles/work-tasks
- Declarative Automation Bundles configuration: https://docs.databricks.com/aws/en/dev-tools/bundles/settings
- Bundle substitutions and variables: https://docs.databricks.com/aws/en/dev-tools/bundles/variables
- Deploy bundles from the workspace: https://docs.databricks.com/aws/en/dev-tools/bundles/workspace-deploy

