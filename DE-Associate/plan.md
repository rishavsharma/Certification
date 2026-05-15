# Databricks DE Associate Current Exam Learning Modules Plan

## Summary
- Build a new study track for the **current** Databricks Certified Data Engineer Associate blueprint that is valid **through May 3, 2026**.
- Use **5 parallel subagents/workers**, one per exam section, with strict folder ownership and no overlap.
- Each worker executes the same 2-step flow:
  1. Research official Databricks material and write the section study guide.
  2. Write the section quiz and a separate answers file.
- Deliver **5 section modules** and **252 total exam-style MCQs**:
  - Section 1: 50
  - Section 2: 50
  - Section 3: 52
  - Section 4: 50
  - Section 5: 50
- Primary sources to anchor the content:
  - [Current exam guide PDF](https://www.databricks.com/sites/default/files/2026-03/databricks-certified-data-engineer-associate-exam-guide-may-4-2026.pdf)
  - [Databricks Connect docs](https://docs.databricks.com/aws/en/dev-tools/databricks-connect)
  - [Auto Loader docs](https://docs.databricks.com/aws/en/ingestion/cloud-object-storage/auto-loader)
  - [Bundles docs](https://docs.databricks.com/en/dev-tools/bundles/index.html)
  - [Lakeflow Jobs docs](https://docs.databricks.com/workflows/jobs/settings.html)
  - [Spark UI guide](https://docs.databricks.com/en/optimizations/spark-ui-guide/index.html)
  - [Data governance / Unity Catalog docs](https://docs.databricks.com/aws/en/data-governance)
  - [Delta Sharing docs](https://docs.databricks.com/aws/en/delta-sharing)

## Output Interface
- No code/API changes are needed. The deliverable interface is the markdown content tree below.

```text
current-exam-through-2026-05-03/
  README.md

  section-01-databricks-intelligence-platform/
    01_blueprint-and-study-scope.md
    02_data-layout-and-query-performance.md
    03_platform-value-and-core-components.md
    04_compute-selection-decision-guide.md
    quiz.md
    answers.md

  section-02-development-and-ingestion/
    01-databricks-connect.md
    02-notebooks-capabilities.md
    03-auto-loader.md
    04-debugging-toolbelt.md
    quiz.md
    answers.md

  section-03-data-processing-transformations/
    01-medallion-architecture.md
    02-compute-and-cluster-choice.md
    03-lakeflow-spark-declarative-pipelines-ldp.md
    04-delta-ddl-dml-and-pyspark-aggregations.md
    quiz.md
    answers.md

  section-04-productionizing-data-pipelines/
    01-dabs-and-asset-bundles.md
    02-deploying-and-operating-workflows-lakeflow-jobs.md
    03-serverless-and-spark-ui-troubleshooting.md
    quiz.md
    answers.md

  section-05-data-governance-quality/
    00-section-brief.md
    01-uc-tables-roles-permissions.md
    02-audit-logs-lineage.md
    03-delta-sharing-lakehouse-federation.md
    quiz.md
    answers.md
```

- `README.md` states this track is for exams taken on or before **May 3, 2026** and links the 5 section folders.
- Every guide file follows one template: scope/objectives, terminology drift note, core concepts, examples or decision tables, common exam traps, and official-source links.
- Every `quiz.md` contains questions only, numbered, with 4 options and one best answer.
- Every `answers.md` contains question number, correct option, mapped objective, and a brief rationale.
- Keep current-exam wording visible, but normalize terminology drift inline:
  - cluster -> compute
  - DAB -> Declarative Automation Bundles
  - workflow -> Lakeflow Jobs
  - DLT -> Lakeflow Spark Declarative Pipelines

## Section Ownership And Content
- Worker 1 owns `section-01-databricks-intelligence-platform/`. Focus on platform value, data layout/performance features, and compute selection. Weight the 50 questions toward layout/performance and compute choice.
- Worker 2 owns `section-02-development-and-ingestion/`. Focus on Databricks Connect, notebook capabilities, Auto Loader use cases/syntax, and built-in debugging tools. Weight the 50 questions toward notebooks and Auto Loader.
- Worker 3 owns `section-03-data-processing-transformations/`. Focus on Medallion architecture, compute/cluster choice, Lakeflow Spark Declarative Pipelines, DDL/DML, and PySpark aggregations. Use 52 questions because this section is the broadest.
- Worker 4 owns `section-04-productionizing-data-pipelines/`. Focus on DAB/bundle structure, workflow deployment and repair/rerun, serverless compute, and Spark UI interpretation. Weight the 50 questions toward bundles and workflow operations.
- Worker 5 owns `section-05-data-governance-quality/`. Focus on managed vs external tables, Unity Catalog roles and grants, audit logs, lineage, Delta Sharing, and Lakehouse Federation. Weight the 50 questions toward grants, table types, and Delta Sharing.
- Exclude **post–May 4, 2026** objectives from the current track except as terminology notes. Specifically do not teach ABAC, row filters, column masks, or `DENY` as current-exam objectives.

## Execution Workflow
- Phase A per worker: read the relevant section from `de-associate-cource.txt`, research only official Databricks sources, and write the guide files for that section.
- Phase B per worker: write `quiz.md` from the completed guide, then write `answers.md` from the quiz.
- Run one coordinator pass after all workers finish to remove overlap, standardize terminology, align markdown formatting, and ensure the difficulty stays at associate level.
- Do not allow workers to edit outside their assigned section folder, except the coordinator updating the shared `README.md`.

## Test Plan
- Coverage check: every bullet in the current blueprint valid through **May 3, 2026** appears in guide content and in at least one quiz question.
- File check: every section folder contains the exact planned guide files plus `quiz.md` and `answers.md`.
- Quiz check: each section meets its target count, each question has 4 options, and each question has exactly one correct answer.
- Answer-key check: the number of answers matches the number of quiz questions in every section.
- Scope check: no new-exam-only topic bleeds into the current track beyond explicit terminology mapping notes.
- Quality check: review at least 10 questions per section for ambiguity, duplicate distractors, or multiple plausible answers.
- Consistency check: section terminology mappings do not conflict across folders, and source links stay on official Databricks properties.

## Assumptions
- Output language is English.
- The exam target is before **May 4, 2026**, so the current blueprint is the source of truth.
- The later instruction to allow **5 subagents** overrides the earlier “no more than 4” constraint.
- Answers files include brief rationales because that improves study value without changing the exam-style MCQ format.
