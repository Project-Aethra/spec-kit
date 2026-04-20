# CLAUDE.md — spec-kit

## What This Repo Is

`spec-kit` is Magnon Compute Corporation's local mirror of the upstream
[github/spec-kit](https://github.com/github/spec-kit) project — a toolkit for
Spec-Driven Development (SDD). It ships the **Specify CLI** (`specify`), which bootstraps
projects with a `.specify/` scaffold, spec templates, AI agent command files, and workflow
integrations.

This repo **tracks upstream only**. MagnonOS-specific extensions live in the separate
`magnon-spec-kit` repo (`Project-MagnonOS/magnon-spec-kit`). Do not add Magnon-specific
code here.

## Tech Stack

- **Language:** Python 3.12+
- **CLI framework:** [Typer](https://typer.tiangolo.com/) + [Rich](https://rich.readthedocs.io/)
- **HTTP:** `httpx` (with `truststore` for system CA trust)
- **Input:** `readchar` (cross-platform keyboard input)
- **Build:** `hatchling`
- **Package name:** `specify-cli`
- **Entry point:** `specify` → `specify_cli:main`

## Repo Structure

```
src/specify_cli/
  __init__.py           Main CLI — AGENT_CONFIG dict, all commands (init, check, update)
templates/              Spec templates used when bootstrapping a project
  spec-template.md
  plan-template.md
  tasks-template.md
  checklist-template.md
  agent-file-template.md
  commands/             AI agent command files (one per supported agent)
  vscode-settings.json
scripts/
  bash/                 update-agent-context.sh
  powershell/           update-agent-context.ps1
.github/workflows/
  release.yml           Automated release pipeline
  scripts/              create-release-packages.sh, create-github-release.sh
```

## Dev Commands

```bash
# Install in editable mode
uv sync

# Run the CLI directly
uv run specify init --ai claude
uv run specify check
uv run specify --help

# Install as global tool
uv tool install .
specify init .
```

## Key Invariants

- **AGENT_CONFIG keys must match the actual CLI executable name** (e.g. `"cursor-agent"`,
  not `"cursor"`). The `check_tool()` function uses `shutil.which(key)` directly. Using a
  shorthand key requires special-case mappings everywhere — do not do this.
- **Any change to `src/specify_cli/__init__.py` requires** a version bump in `pyproject.toml`
  and a new entry in `CHANGELOG.md`.
- **When adding a new agent**, all of these must be updated consistently:
  1. `AGENT_CONFIG` dict in `__init__.py`
  2. `--ai` parameter help text
  3. `README.md` Supported AI Agents table
  4. `.github/workflows/scripts/create-release-packages.sh` (ALL_AGENTS array + case stmt)
  5. `.github/workflows/scripts/create-github-release.sh` (release asset list)
  6. `scripts/bash/update-agent-context.sh`
  7. `scripts/powershell/update-agent-context.ps1`
- **`requires_cli`** in AGENT_CONFIG: set `True` only for agents that have an actual CLI
  tool to check via `shutil.which`; set `False` for IDE-based agents.
- The `upstream` remote points to `https://github.com/github/spec-kit.git`. Pull upstream
  improvements via `git fetch upstream && git merge upstream/main`.

## Supported Agent Formats

| Format   | Agents                                  | Argument placeholder |
|----------|-----------------------------------------|----------------------|
| Markdown | Claude, Cursor, opencode, Windsurf, Q   | `$ARGUMENTS`         |
| TOML     | Gemini, Qwen                            | `{{args}}`           |

Script placeholder `{SCRIPT}` is replaced with the actual script path at init time.

## What NOT to Do

- Do not add MagnonOS-specific code (org scaffolding, `@include` resolution, Magnon
  templates). That belongs in `Project-MagnonOS/magnon-spec-kit`.
- Do not use a shorthand AGENT_CONFIG key that differs from the real executable name.
- Do not skip the version bump + CHANGELOG entry when modifying `__init__.py`.
- Do not force-push to `main` or `upstream`-tracking branches.
- Do not commit credentials or API tokens.
