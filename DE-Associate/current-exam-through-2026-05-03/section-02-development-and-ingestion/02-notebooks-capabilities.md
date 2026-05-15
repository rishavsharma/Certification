# Notebook Functionality and Capabilities (Section 2)

## Scope / Objectives (Current exam through May 3, 2026)

Current-exam objective:
- Determine the capabilities of Notebooks functionality.

What this guide covers for the objective:
- Notebook languages, cell types, and magic commands.
- Collaboration, sharing, comments, and version history.
- Parameterization (widgets) and modularization (`%run`, sharing code).
- Import/export formats and known UI constraints that affect behavior in questions.

## Terminology Drift Note (Keep current-exam wording visible)

- Docs often say "compute" and "access modes" instead of "clusters". Expect both terms in exam questions.
- "Databricks Assistant" has been rebranded in parts of the docs as "Genie Code". This is not a core exam objective, but it may appear in UI-oriented questions.
- Notebooks exist as several file formats (`.ipynb`, source `.py`, `.sql`, `.scala`, `.r`, and Databricks archives). Some questions mix "notebook" with "file in Repos/Git folders"; know the differences.

## Core Concepts

### What notebooks are (capability summary)

Databricks notebooks provide:
- Multi-language authoring (Python, SQL, Scala, R) including language switching per cell.
- Real-time coauthoring, comments, and sharing.
- Automatic versioning with diffs and restore.
- Built-in visualizations and interactive results tables.
- Execution controls (run cell, run all, etc.) on attached compute.

### Languages and execution contexts

Supported primary language magics:
- `%python`, `%sql`, `%scala`, `%r`

Important behavior:
- Each language runs in its own REPL/execution context.
- Variables defined in one language are not automatically available in another language.
- Cross-language state sharing is typically done via external artifacts (tables, files, views).

Auxiliary magic commands (frequent exam targets):
- `%md` for Markdown documentation.
- `%sh` to run shell commands on the driver node only.
- `%fs` as shorthand for filesystem commands (for example `%fs ls` is similar to `dbutils.fs.ls`).

### Widgets (parameterization)

Widgets are input parameters you can define in the UI or programmatically via `dbutils.widgets`.

Widget types (Databricks widgets):
- `text`
- `dropdown`
- `combobox`
- `multiselect`

Common usage patterns:
- Pass parameters into a notebook or dashboard run.
- Re-run the same notebook with different widget values.

### Modularization and notebook orchestration

Two common patterns:
- `%run /path/to/notebook` to execute another notebook inline and import functions/variables it defines.
- "Share code between notebooks" by creating workspace source files (Python/R modules) and importing them, or packaging code into a library.

Key `%run` rule:
- `%run` must be the only command in its cell because it executes the entire target notebook inline.

### Version history and collaboration

Notebook version history:
- Notebooks maintain version snapshots.
- You can add descriptions, restore, delete, and clear history.
- Side-by-side diffs help compare versions.

Collaboration:
- Real-time collaboration (multiple editors).
- Comments and comment management.

### Import/export formats (and why they matter on exams)

Supported import/export formats include:
- Source files: `.py`, `.sql`, `.scala`, `.r` (code-only)
- HTML export
- Databricks archive `.dbc`
- Jupyter `.ipynb`
- RMarkdown `.Rmd`

Exam-relevant detail:
- Export can optionally include outputs for some formats (HTML, IPYNB, DBC) if outputs were not cleared.
- There are size limits and UI output limits that can change what you observe during interactive debugging.

### Notebook limits that commonly surface in questions

Examples of practical limits (UI-focused, not Spark limits):
- Results tables are capped in the notebook UI (row count and/or total size).
- Individual notebook cells have an input size limit.
- Notebook output sizes can be capped, especially for job runs.

Practical exam takeaway:
- If a question says "the notebook only shows 10,000 rows", treat that as a UI constraint, not proof that the query only produced 10,000 rows.

## Examples / Decision Tables

### Magic commands quick table

| Magic | Capability | Key constraint |
|---|---|---|
| `%python`, `%sql`, `%scala`, `%r` | Set cell language | Language REPLs do not share variables by default |
| `%md` | Documentation cell | No code execution |
| `%sh` | Run shell command on driver | Does not run on workers |
| `%fs` | File system convenience | Equivalent to `dbutils.fs` patterns |
| `%run` | Execute another notebook inline | Must be in a cell by itself |

### Widgets example (Python)

```python
# Define a widget (id: "state") and read its value
dbutils.widgets.text("state", "CA")
state = dbutils.widgets.get("state")

df = spark.table("some_catalog.some_schema.some_table").where(f"state = '{state}'")
display(df)
```

### Choose the right artifact table

| You need to... | Best notebook feature | Why |
|---|---|---|
| Parameterize a notebook run | Widgets | First-class parameter UI and API |
| Reuse code across notebooks quickly | `%run` | Imports notebook-defined functions/vars inline |
| Reuse production code across notebooks and jobs | Workspace files or libraries | Proper packaging and import semantics |
| Audit or recover prior work | Version history | Restore snapshots and view diffs |
| Collaborate live | Real-time coauthoring + comments | Built-in sharing and review |

## Common Exam Traps

- Expecting variables to cross language boundaries: `%sql` variables do not automatically see `%python` variables, and vice versa.
- Thinking `%sh` runs on all nodes: it runs on the driver only. For all-nodes setup, init scripts are the pattern (not a notebook magic).
- Misusing `%run`: it must be the only command in the cell and it runs the entire notebook inline.
- Forgetting widget permission requirement: to add or edit widgets, you need edit permissions on the notebook.
- Misreading notebook outputs: table results and output sizes have limits; absence of rows in the UI is not proof a streaming query read nothing.
- Confusing formats: a `.py` source export is not a rich `.ipynb` notebook; exporting a notebook can include outputs depending on format and settings.

## Official-Source Links (Databricks)

- Exam guide PDF that includes the current (through May 3, 2026) Section 2 objectives: https://www.databricks.com/sites/default/files/2026-03/databricks-certified-data-engineer-associate-exam-guide-may-4-2026.pdf
- Databricks notebooks overview: https://docs.databricks.com/en/notebooks/index.html
- Develop code in Databricks notebooks (languages, magics, formatting, versioning): https://docs.databricks.com/notebooks/notebooks-use.html
- Code languages and magic commands in notebooks: https://docs.databricks.com/aws/en/notebooks/notebooks-code
- Databricks widgets: https://docs.databricks.com/aws/en/notebooks/widgets
- Orchestrate notebooks and modularize code (`%run` and patterns): https://docs.databricks.com/aws/en/notebooks/notebook-workflows
- Collaborate using Databricks notebooks (sharing, comments): https://docs.databricks.com/aws/en/notebooks/notebooks-collaborate
- Version history in notebooks (diffs, restore): https://docs.databricks.com/aws/en/notebooks/notebook-version-history
- Import and export Databricks notebooks (formats, outputs): https://docs.databricks.com/gcp/en/notebooks/notebook-export-import
- Known limitations of Databricks notebooks (output and sizing limits): https://docs.databricks.com/aws/en/notebooks/notebook-limitations
