# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repository is

`eng-mgr-toolkit` is a **BMAD Method v6.9.0 workspace** for engineering-management work, not a conventional codebase. There is no application/product code yet — `docs/` and `_bmad-output/` are empty, and only `README.md` is committed to git (`.claude/` and `_bmad/` are currently untracked). Work in this repo is driven by **BMAD skills** (invoked as slash commands / via the Skill tool), which produce documents and, eventually, code.

The toolkit installs two BMAD modules: **core** (brainstorming, party-mode, help) and **bmm** ("BMad Method" — the software-delivery lifecycle). README points to a project Wiki for usage instructions.

## How work flows (BMAD phases)

BMM skills are organized into a phased lifecycle. The phase and two-letter menu code for every skill live in `_bmad/bmm/module-help.csv` (and `_bmad/core/module-help.csv`). The intended progression:

1. **1-analysis** — brainstorming (BP), market/domain/technical research (MR/DR/TR), product brief (CB) or PRFAQ (WB)
2. **2-planning** — PRD (PRD), UX design (CU)
3. **3-solutioning** — architecture spine (CA), epics & stories (CE), implementation-readiness check (IR)
4. **4-implementation** — sprint planning (SP) → create story (CS) → validate story (VS) → dev story (DS) → code review (CR), with retrospective (ER) at epic end. Plus `anytime` skills: quick-dev (QQ), investigate (IN), checkpoint (CK), QA e2e tests (QA), correct-course (CC).

The `preceded-by` / `followed-by` / `required` columns in `module-help.csv` define the dependency graph between skills — consult it to know what a given skill expects to already exist and what comes next. Use the `bmad-help` skill to get routed to the right next step.

**Agent personas** (see `[agents.*]` in `_bmad/config.toml`): Mary (analyst), John (PM), Sally (UX), Winston (architect), Amelia (dev), Paige (tech-writer). Each maps to a `bmad-agent-*` skill.

## Where outputs go

Configured in `_bmad/bmm/config.yaml` / `_bmad/core/config.yaml`:
- `_bmad-output/planning-artifacts/` — briefs, PRDs, UX, architecture, epics/stories
- `_bmad-output/implementation-artifacts/` — sprint status, stories, specs, test suites
- `docs/` — project knowledge (the `project_knowledge` location)

Each skill's `output-location` in `module-help.csv` says where its artifact belongs.

## Configuration — what is and isn't editable

**Installer-managed (treat as read-only; regenerated on every install):**
- `_bmad/config.toml`, `_bmad/config.user.toml`
- `_bmad/core/config.yaml`, `_bmad/bmm/config.yaml`
- each skill's `customize.toml`

**Human-authored overrides (safe to edit; never touched by installer):**
- `_bmad/custom/config.toml` — team config overrides, **committed**
- `_bmad/custom/config.user.toml` — personal config overrides, **gitignored** (`*.user.toml`)
- `_bmad/custom/<skill-name>.toml` / `.user.toml` — per-skill workflow overrides

To change a setting durably, edit the matching `_bmad/custom/` file (or re-run the installer) — do **not** edit the installer-managed files.

### Config resolution

Config is a layered TOML merge (highest priority last): base team → base user → custom team → custom user. Per-skill customization merges: skill `customize.toml` → `custom/<skill>.toml` → `custom/<skill>.user.toml`. Merge rules: scalars override, tables deep-merge, arrays-of-tables keyed by `code`/`id` merge by key, other arrays append. There is no removal mechanism.

Resolve config programmatically with the stdlib-only scripts in `_bmad/scripts/` (require Python 3.11+ for `tomllib`):

```bash
uv run _bmad/scripts/resolve_config.py --project-root . [--key core|agents|...]
uv run _bmad/scripts/resolve_customization.py --skill .claude/skills/<skill-dir> [--key ...]
```

`python3` also works during the transition; BMAD is standardizing on `uv run`.

## Skill structure & tests

Each skill lives in `.claude/skills/<name>/` with a `SKILL.md` (entry point), optional `steps/`, `references/`, `assets/`, `templates/`, and `scripts/`. Some skills ship Python helpers with tests under `scripts/tests/`:

```bash
# run a single skill's script tests
uv run pytest .claude/skills/bmad-architecture/scripts/tests/test_lint_spine.py
```

There is no repo-wide build, lint, or test runner — tooling is per-skill and stdlib-based.
