# AGENTS.md (English Translation)

> This is a reference translation of [AGENTS.md](AGENTS.md). `AGENTS.md` (Chinese) remains the single source of truth for AI collaboration rules in this repository per Section 2; if this translation and the Chinese original ever disagree, `AGENTS.md` governs. When `AGENTS.md` changes, this file should be reviewed for drift.

This file constrains the default development workflow for this repository. The goal is to reduce repeated back-and-forth, reduce rework, and keep changes consistent with the current project structure.

If this file is inconsistent with the repository's actual scripts, workflows, or code, treat the actual, runnable state as authoritative, and fix this document as part of the related change to avoid the rules drifting further out of sync.

## 1. Hard Rules

- Respect existing directory boundaries:
  - Backend logic goes primarily in `src/`, `data_provider/`, `api/`, `bot/`
  - Web frontend changes go in `apps/dsa-web/`
  - Desktop changes go in `apps/dsa-desktop/`
  - Deployment and pipeline changes go in `scripts/`, `.github/workflows/`, `docker/`
- Do not run `git commit`, `git tag`, or `git push` without explicit confirmation.
- Commit messages are in English; do not add `Co-Authored-By`.
- Do not hardcode secrets, accounts, paths, model names, ports, or environment-specific branching logic.
- Prefer reusing existing modules, config entry points, scripts, and tests; do not add parallel implementations.
- Stability takes priority over "opportunistic optimization" by default; refactors, abstractions, and infrastructure migrations not directly required by the current task should be avoided.
- When adding a new config option, update `.env.example` and related documentation in the same change.
- Changes to user-visible capabilities, CLI/API behavior, deployment methods, or notification methods, or report structure changes, must be accompanied by updates to the relevant docs and `docs/CHANGELOG.md`.
- When changing report format, report rendering, or the Web UI, the PR description must include screenshots of the affected report(s)/page(s); prefer before/after comparisons when there's a visible diff, and explain why plus provide alternative visual evidence if a screenshot isn't possible.
- Issue/PR process screenshots, review screenshots, one-off acceptance screenshots, and other temporary visual evidence must not be committed as repository files; they belong in the PR description, PR comments, GitHub attachments, Actions artifacts, or an externally accessible evidence link. The exception is diagrams that genuinely need to be kept as long-term product documentation — but their filenames and documentation semantics must be decoupled from any specific issue/PR number.
- The `[Unreleased]` section of `docs/CHANGELOG.md` uses a **flat format**: each entry is its own line, formatted as `- [type] description`, where type is one of `新功能`(feature)/`改进`(improvement)/`修复`(fix)/`文档`(docs)/`测试`(test)/`chore`; **do not add `### category headings`** inside `[Unreleased]`, to reduce merge conflicts across concurrent PRs. At release time, the maintainer consolidates entries into the formal, headed format.
- `README.md` is only for project positioning, a high-level capability overview, quick start, main entry points, and sponsorship/partnership info — homepage-level content. Don't update the README unless necessary, to avoid unbounded growth.
- Finer-grained module behavior, page interactions, feature-specific configuration, troubleshooting notes, field contracts, implementation semantics, and edge cases should go into the relevant `docs/*.md` or topic-specific docs, not the README.
- When changing one of a bilingual (Chinese/English) doc pair, evaluate whether the other one needs to be updated too; if you don't sync it, state why in the delivery notes.
- Comments, docstrings, and log messages should be clear and accurate; English is not mandatory, but should stay consistent with the surrounding file's language.

## 1.1 PR Title Convention (Non-blocking Recommendation)

- Recommended PR title format: `<type>: <what changed>`, e.g. `fix: 修复大盘分析历史记录丢失`, preferring types `fix`/`feat`/`refactor`/`docs`/`chore`/`test`/`ci`.
- The title should describe the actual change; avoid adding `[codex]`, `codex`, `autocode`, `copilot`, or other tool/agent origin prefixes.
- This convention is only a collaboration readability/consistency nudge and should not by itself be a review process blocker.

## 1.2 Contribution Quality Floor

- This repository does not accept PRs that substitute stacking code volume, inflating diff surface area, or patch-style responses to review, for genuine design convergence.
- Contribution quality is judged by whether it solves a clearly defined problem, minimizes blast radius, keeps existing contracts consistent, and covers real risk paths — not by lines added, file count, feature marketing, or "looking complete."
- Please do not treat this repository as a low-cost experimentation ground, a resume showcase, or a contribution-farming venue. Every PR must demonstrate that its author understands the current system's contracts and has completed basic self-review, integration, and verification.
- Using AI assistance to develop code is not itself a problem; the problem is submitting AI-generated code that hasn't undergone human semantic review, hasn't been verified, and hasn't converged. Such PRs will be treated as low-quality submissions.
- After review feedback, only appending a localized patch at the exact spot called out by the reviewer is not acceptable. The author must re-check all entry points, configuration, tests, docs, workflows, and user-visible paths touched by the same business semantics.
- If a PR keeps exhibiting the same kind of contract drift, repeated fallbacks, tests that bypass the real risk layer, or a PR body that doesn't match the actual diff across multiple rounds of review, maintainers may ask for it to be closed and redone instead of continuing point-by-point review.

## 2. AI Collaboration Asset Governance

- `AGENTS.md` is the single source of truth for AI collaboration rules in this repository.
- `CLAUDE.md` must be a symlink pointing to `AGENTS.md`, for compatibility with the Claude ecosystem.
- `.github/copilot-instructions.md` and `.github/instructions/*.instructions.md` are mirrors or layered supplements for GitHub Copilot / Coding Agent; if they conflict with this file, `AGENTS.md` takes precedence.
- Repository collaboration skills live in `.claude/skills/`; analysis artifacts live in `.claude/reviews/`. The former can be committed; the latter is treated as local-only by default.
- The root `SKILL.md` and `docs/openclaw-skill-integration.md` are product or external-integration documentation, not a source of truth for repository collaboration rules.
- If a new `.agents/skills/` or other agent-specific directory is introduced in the future, first clarify a single source of truth, then sync via script or mirroring; do not manually maintain multiple long-lived copies of equivalent content.
- When modifying AI collaboration governance assets, run:

```bash
python scripts/check_ai_assets.py
```

## 3. Repository Overview

- Project positioning: an intelligent stock analysis system covering A-shares, Hong Kong stocks, and US stocks.
- Main pipeline: fetch data -> technical analysis / news retrieval -> LLM analysis -> generate report -> push notifications.
- Key entry points:
  - `main.py`: main entry point for analysis jobs
  - `server.py`: FastAPI service entry point
  - `apps/dsa-web/`: Web frontend
  - `apps/dsa-desktop/`: Electron desktop app
  - `.github/workflows/`: CI, release, and daily jobs
- Core responsibilities:
  - `src/core/`: main pipeline orchestration
  - `src/services/`: business service layer
  - `src/repositories/`: data access layer
  - `src/reports/`: report generation
  - `src/schemas/`: schemas / data structures
  - `data_provider/`: multi-source adaptation and fallback
  - `api/`: FastAPI API
  - `bot/`: bot integrations
  - `scripts/`: local scripts
  - `.github/scripts/`: GitHub automation scripts
  - `tests/`: pytest tests
  - `docs/`: documentation and notes

## 4. Common Commands

### Run the app

```bash
python main.py
python main.py --debug
python main.py --dry-run
python main.py --stocks 600519,hk00700,AAPL
python main.py --market-review
python main.py --schedule
python main.py --serve
python main.py --serve-only
uvicorn server:app --reload --host 0.0.0.0 --port 8000
```

### Backend verification

```bash
pip install -r requirements.txt
pip install flake8 pytest
./scripts/ci_gate.sh
python -m pytest -m "not network"
python -m py_compile <changed_python_files>
```

### Web / Desktop

```bash
cd apps/dsa-web
npm ci
npm run lint
npm run build

cd ../dsa-desktop
npm install
npm run build
```

### PR / CI evidence

```bash
gh pr view <pr_number>
gh pr checks <pr_number>
gh run view <run_id> --log-failed
```

## 5. Default Workflow

1. First determine the task type: `fix / feat / refactor / docs / chore / test / review`
2. Read the existing implementation, configuration, tests, scripts, workflows, and docs before making changes.
3. Identify the change boundary: backend / API / Web / Desktop / Workflow / Docs / AI collaboration assets.
4. Determine up front whether the change touches a high-risk area: config semantics, API/schema, data-source fallback, report structure, auth, scheduling, release process, desktop startup chain.
5. Make only the minimal change directly relevant to the current task; don't bundle in unrelated refactors.
6. If docs, scripts, or workflow descriptions are inconsistent with reality, trust the actual code/workflow first, then decide whether to also fix the docs.
7. After making changes, run the checks in the verification matrix below.
8. The final delivery should, by default, state:
   - What changed
   - Why it changed
   - Verification performed
   - What was not verified
   - Risks
   - Rollback plan

## 6. Verification Matrix

### CI Coverage Principles

The repository's CI currently consists mainly of:

| Check | Source | Description | Blocking |
| --- | --- | --- | --- |
| `ai-governance` | `.github/workflows/ci.yml` | Validates the relationship between `AGENTS.md` / `CLAUDE.md` / `.github` instructions / `.claude/skills` | Yes |
| `backend-gate` | `.github/workflows/ci.yml` | Runs `./scripts/ci_gate.sh` | Yes |
| `docker-build` | `.github/workflows/ci.yml` | Docker build and smoke-import of key modules | Yes |
| `web-gate` | `.github/workflows/ci.yml` | Runs `npm run lint` + `npm run build` when frontend changes | Yes (when triggered) |
| `network-smoke` | `.github/workflows/network-smoke.yml` | `pytest -m network` + `scripts/test.sh quick` | No, observational |
| `pr-review` | `.github/workflows/pr-review.yml` | PR static checks + AI review + auto-labeling | No, auxiliary |

If a PR already has the relevant CI results, you can cite the CI conclusion directly; if CI doesn't cover the change surface, or the local environment differs significantly from CI, supplement with local verification and note the gap.

### Execute Based on Change Surface

- Python backend changes:
  - Scope: `main.py`, `src/`, `data_provider/`, `api/`, `bot/`, `tests/`
  - Run first: `./scripts/ci_gate.sh`
  - Minimum requirement: `python -m py_compile <changed_python_files>`
  - If the change affects the API, task orchestration, report generation, notification sending, data-source fallback, auth, or scheduling, the delivery notes must state whether those paths were covered.

- Web frontend changes:
  - Scope: `apps/dsa-web/`
  - Default: `cd apps/dsa-web && npm ci && npm run lint && npm run build`
  - If it involves API integration, routing, state management, Markdown/chart rendering, or auth state, the delivery notes must clearly describe the interaction surface and any uncovered risk.

- Desktop changes:
  - Scope: `apps/dsa-desktop/`, `scripts/run-desktop.ps1`, `scripts/build-desktop*.ps1`, `scripts/build-*.sh`, `docs/desktop-package.md`
  - Default: build the Web app first, then the desktop app
  - If platform limitations prevented full verification, clearly state whether the Web build output, the Electron build, and the Release workflow impact were verified.

- API / Schema / auth-linked changes:
  - Scope: `api/**`, `src/schemas/**`, `src/services/**`, `apps/dsa-web/**`, `apps/dsa-desktop/**`
  - At minimum cover the corresponding backend verification plus a build verification of affected clients.
  - If it touches login, cookies, sessions, polling state, or field/enum additions/removals, compatibility impact must be explicitly stated.

- Docs and governance file changes:
  - Scope: `README.md`, `docs/**`, `AGENTS.md`, `.github/copilot-instructions.md`, `.github/instructions/**`, `.claude/skills/**`
  - Code tests are not mandatory.
  - Confirm commands, config options, filenames, and workflow names match the actual repository.
  - When modifying AI collaboration governance assets, run `python scripts/check_ai_assets.py`.

- Workflow / script / Docker changes:
  - Scope: `.github/**`, `scripts/**`, `docker/**`
  - Run the local verification closest to the change surface.
  - In the delivery notes, state which pipeline, release path, or deployment path is affected.
  - If Docker / GitHub Actions verification wasn't run, clearly state why and the potential risk.

- Network- or third-party-dependency-related changes:
  - Run offline or deterministic checks first.
  - Prioritize confirming that timeout, retry, fallback, error messaging, and degradation paths still hold.
  - If online verification wasn't run, you must clearly state why.

## 7. Stability Guardrails

- Config and runtime entry points:
  - When changing `.env` semantics, default values, CLI arguments, service startup method, or scheduling semantics, evaluate the impact on local runs, Docker, GitHub Actions, API, Web, and Desktop together.
  - New config should default to "works without configuration, enhanced with configuration," avoiding stacked switches and mutually exclusive modes.

- Data sources and fallback:
  - When changing `data_provider/`, pay attention to data-source priority, failure degradation, field normalization, caching, and timeout strategy.
  - A single data source failing should not take down the whole analysis pipeline, unless the requirement explicitly calls for fail-fast.

- API / Web / Desktop compatibility:
  - When changing API/schema/auth/report payloads, check backend, Web, and Desktop compatibility together.
  - Default to additive fields, keeping old fields, or providing a compatibility layer, to avoid silently breaking existing clients.

- Reports / prompts / notifications:
  - When changing report structure, prompts, extractors, notification templates, or bot integration chains, check that upstream inputs and downstream consumers remain compatible.
  - A single notification channel failing should not take down the whole analysis pipeline, unless the requirement explicitly calls for fail-fast.
  - When changing `EXTRACT_PROMPT` in `src/services/image_stock_extractor.py`, include the full, updated prompt in the PR description.

- Workflows / release / packaging:
  - When changing auto-tagging, Release, Docker publishing, the daily analysis job, or the desktop packaging flow, evaluate trigger conditions, artifact paths, permission boundaries, and rollback method.
  - Auto-tagging defaults to opt-in: a version bump is only triggered when the commit title contains `#patch`, `#minor`, or `#major`, unless the requirement explicitly calls for changing the release strategy.

## 8. Issue / PR / Skill Workflow

- The repository already has the following skills; prefer reusing them:
  - `.claude/skills/analyze-issue/SKILL.md`
  - `.claude/skills/analyze-pr/SKILL.md`
  - `.claude/skills/fix-issue/SKILL.md`
- If the task is clearly issue analysis, PR review, or an issue fix, prefer following the corresponding skill, and save artifacts to `.claude/reviews/`.
- The commands, templates, verification order, and delivery structure inside a skill must stay consistent with `AGENTS.md`.
- Before every PR creation/update, PR review, or issue analysis, first sync to the latest baseline: check the working tree state and run `git fetch --all --prune`; if the working tree is clean and the current branch can fast-forward, run `git pull --ff-only`. If there are local changes, a conflicted state, untracked risk files, or the branch cannot fast-forward, do not forcibly switch branches, stash, reset, or overwrite local state; PR review / issue analysis can instead use the already-fetched remote refs/PR head for analysis, and must clearly record in the analysis doc why the local working tree wasn't updated, the current local HEAD, and the remote baseline used; PR creation/update should first state the divergence between the current branch and the target baseline, and if necessary ask the user to confirm rebase, merge, or continuing on the current branch.
- Skills should default to reading CI/workflow evidence first, then decide whether to supplement with local verification.
- Other than the safe fast-forward sync described above for PR creation/update and PR review/issue analysis, skills must not by default run `git pull`, `git push`, `git tag`, `gh pr create`, or other operations that change remote or current-branch state; these operations require user confirmation.
- Default PR review order:
  1. Necessity
  2. Relevance
  3. Title suggestion (`<type>: <what changed>`, without a tool/agent prefix; not a hard blocker)
  4. Description completeness (checked against `.github/PULL_REQUEST_TEMPLATE.md`)
  5. Verification evidence
  6. Implementation correctness
  7. Merge decision
- For `fix`-type PRs, you must state: the original problem, root cause, the fix, and regression risk.
- Merge-blocking conditions:
  - Correctness or security issues
  - Blocking CI not passing
  - The PR description substantively contradicts the actual change
  - Missing a rollback plan
  - Repeated, unconverged contract drift, patch stacking, or misrepresented verification evidence

## 8.1 Handling Review Feedback and the Ban on Patch Stacking

When handling review feedback, you may not simply append a localized patch at the spot the reviewer called out and then claim "fully fixed." You must first re-understand the business contract the reviewer identified, then check all entry points, configuration, tests, docs, workflows, and user-visible paths touched by that same semantics.

After receiving review feedback, proceed in this order:

1. List out every issue the reviewer raised, one by one.
2. Explain the root cause — not just describe "which lines changed."
3. Identify every related path affected by the same semantics, e.g. runtime, API/Web, CLI, diagnostics, workflow, docs, tests.
4. Fix the full contract, not just the currently failing test or the specific commented line.
5. Add regression tests covering the reviewer's counterexamples, final end-to-end verification, or clearly state why it can't be verified.
6. Update the PR body accordingly, so scope, verification results, compatibility, risk, and rollback plan stay consistent with the current head.

If you cannot bring this to convergence, do not keep stacking patches, and do not claim it's ready for merge. Instead, proactively state that the PR needs to be split, closed and redone, or ask the maintainer to confirm a new, smaller scope.

The following behaviors are treated as low-quality PRs:

- Using broad fallbacks, silent degradation, or `return False/None/[]` to paper over an unclear contract.
- Tests that mock out the real risk layer, only proving the local implementation passes.
- Claiming an issue is closed once CI passes, without covering the counterexample the reviewer raised.
- A PR body that's inconsistent with the actual diff, verification results, or compatibility risk.
- Continuing to append scattered patches after review instead of re-converging the full semantics.
- The same business semantics behaving inconsistently across runtime, Web/API, docs, workflow, and tests.

CI passing only shows automated checks passed — it cannot substitute for human semantic convergence, nor can it alone prove that the reviewer's counterexample has been closed.

## 9. Delivery and Release

- Default delivery structure:
  - `What changed`
  - `Why it changed`
  - `Verification performed`
  - `What was not verified`
  - `Risks`
  - `Rollback plan`
- For a `docs`-only task, it's fine to simply state: `Docs only, tests not run`, but you must still state whether commands and filenames were checked.
- Auto-tagging does not trigger by default; a version bump is only triggered when the commit title contains `#patch`, `#minor`, or `#major`.
- Manually created tags must be annotated tags.
- User-visible changes should be merged via PR, with labels and verification notes filled in.
