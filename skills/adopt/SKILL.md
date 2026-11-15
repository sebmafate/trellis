---
name: adopt
description: Bring an existing, already advanced project under Trellis by reconstructing its documents from the code, tests, CI and git history: the project's CLAUDE.md with verified commands, STATUS, ARCHITECTURE with retroactive ADRs, a SPEC of what exists and what is next, a TESTING baseline and a PLAN of the remaining work only. Everything reconstructed is marked "to confirm" with evidence and confidence, and the code is never modified. Use when the user wants to use Trellis on a project that already has code ("this project is well advanced", "document this project", "onboard this repo", "reverse-engineer the architecture"). For a new idea use `incubate` instead.
---

# Adopt

Take a project that **already exists** and give it the Trellis documents, so the other skills (`slice`, `deliver`, `autopilot`, `change-course`...) can work on it. The code is the evidence; the documents are **hypotheses** until the user confirms them. Output: a set of documents marked *reconstructed*, plus an adoption report listing what is certain, what is inferred and what is still open.

## Principles

- **Never modify the code.** No edits to source, tests, configuration or build files. You write only Trellis documents, and only after the user has seen them.
- **Evidence over invention.** Every statement in a reconstructed document traces to a file, a commit or something the user said. Mark each item with its **confidence**: `high` (directly visible in code or config), `medium` (inferred from several signals), `low` (a guess). Quote the source (path, commit) where it helps.
- **The code cannot tell the *why*.** Intent, rationale, rejected alternatives, users and success criteria are not in the code. Ask for them, derive them only from explicit sources (README, commit messages, comments, issues), or write "rationale unknown, to confirm". Never fabricate a justification.
- **Mark everything reconstructed.** Each document carries the banner `Reconstructed on <date> from the code and history. To be confirmed.` Individual items are cleared of that mark only when the user confirms them.
- **Never overwrite existing documents.** If a document already exists, show the differences and merge only what the user accepts.
- **Proportional.** A small tool gets a short architecture and three ADRs; a large product gets its main components, not every class.
- **Draft first, then promote.** Drafts are written to the staging folder `.trellis/adopt/`; the user validates, and only then are they placed in their final locations.
- **One question at a time**, with a recommended answer, and only what the code cannot answer. "I don't know" is a fine answer; it becomes an open question.
- **Soft gates.** Flag a missing prerequisite and its risk, then let the user decide.

## Arguments

| Option | Meaning | Default |
| --- | --- | --- |
| `minimal` / `standard` / `full` | How much to reconstruct (see below) | asked, suggest `standard` |
| `--draft` | No interview: infer what can be inferred, mark every intent item as an open question, write to staging only. Suited to non-interactive sessions. | off |
| `--promote` | Place the validated drafts from `.trellis/adopt/` into their final locations | off |

**Levels.** `minimal`: the project's `CLAUDE.md` and `STATUS.md`. `standard`: minimal, plus `ARCHITECTURE.md`, retroactive ADRs, a `TESTING.md` baseline and the adoption report. `full`: standard, plus `SPEC.md` (what exists and what is next) and a `PLAN.md` of the remaining work. A `DESIGN.md` for an existing interface is optional and offered separately.

## Steps

### 0. Scope

Confirm the project folder, that it is a git repository, and which Trellis documents already exist (then work in merge mode). Ask which level the user wants, and say what each costs in time. Ensure `.trellis/` is excluded from git (`.git/info/exclude`) before writing the staging folder.

### 1. Inventory (read-only)

Look at the project without changing it, and keep notes with sources:

- **Stack and structure**: languages and frameworks from the manifests, top-level layout, modules and their roles, entry points, size.
- **Commands**: build, test, lint, format and run commands, from manifests, scripts, CI files and the README. Verify cheap ones (for example listing tests, a quick compile check); ask before running anything long or anything that writes outside build folders.
- **Tests**: frameworks, where they live, rough counts, which components have none, signs of instability (skipped or disabled tests).
- **CI and release**: workflows, release scripts, tags, `CHANGELOG`, signing or deployment setup, how and where it is published.
- **Dependencies**: major ones, lockfiles, anything unmaintained or unusual.
- **History**: first and last commit dates, activity over the last 30 and 90 days, most-changed files (hotspots), contributors, branches, conventions in commit messages and branch names.
- **Existing documentation**: README, `docs/`, ADR-like files, inline TODO and FIXME counts.
- **Hygiene**: presence of secrets-like files (report their existence, never their contents).

Show the user a short inventory summary and ask whether anything is wrong or missing.

### 2. Interview (skip with `--draft`)

Ask only what the code cannot tell you, one question at a time, skipping what the inventory and the README already answer:

1. What is the project for, and for whom?
2. What state is it in for the user: released, in use, prototype? Since when?
3. What is **done** that matters, and what is **next**?
4. What hurts: known debt, fragile areas, things you avoid touching?
5. Which choices need explaining that the code does not (why this stack, this storage, this distribution)?
6. Constraints that apply from now on (platform, privacy, budget, time per week).
7. What does success look like from here, and what would make you stop?
8. Conventions that are not written down (style, branching, review, release habits).

In `--draft` mode, turn each of these into an **open question** in the adoption report instead.

### 3. Reconstruct the documents

Produce them in this order, each as a draft in `.trellis/adopt/`, shown to the user for validation. Reuse the other skills' templates (relative to this skill's directory): the project `CLAUDE.md` from `../bootstrap/templates/`, `ARCHITECTURE` from `../architect/templates/`, ADRs from `../adr/templates/`, `TESTING` from `../test-strategy/templates/`, `PLAN` from `../slice/templates/`, `STATUS` from `../incubate/templates/`, `SPEC` from `../incubate/templates/`. Use the project's document language.

1. **`CLAUDE.md`** (the project's own): the map of documents, the **verified** commands, the conventions actually observed (formatting, test naming, branch and commit style), and the rules from the template. Mark unverified commands as such.
2. **`STATUS.md`**: phase (`development` or `maintenance`), where things stand from the inventory and git, the next action from the interview, risks seen.
3. **`ARCHITECTURE.md`** (levels `standard` and up): components and their responsibilities and dependencies, data and storage, two or three key flows traced from entry points, cross-cutting concerns **as observed** (errors, logging, tests, build and release), a Mermaid overview, a decisions index linking the retroactive ADRs, and a "deferred and open" section for the unknowns. Each element carries its evidence and confidence. The traceability table is filled when a `SPEC.md` exists; otherwise note that it is pending.
4. **Retroactive ADRs** (three to eight, for the decisions that shape the project: language and framework, storage, concurrency model, distribution, authentication, major dependencies). Use the `adr` skill's method, with: status `accepted (reconstructed)`; the decision date noted as unknown or inferred from history (for example "first commit touching X on <date>"); context from evidence and the interview; options **only if known** (otherwise "alternatives not documented, to confirm"); the decision as the code shows it; consequences **observed** in the code, including negative ones; "revisit when" from the user, or open.
5. **`TESTING.md` baseline** (`standard` and up): the real test levels and tools, the commands, which components have no tests, the CI gates as they are versus as wished, and a verification matrix that records for each requirement (if a spec exists) the existing tests or "none". Do not invent a strategy; record the state, and list the gaps.
6. **`SPEC.md`** (`full`): requirements **derived from existing features** (`R1`, `R2`...), each marked `implemented`, with priority (core features are `Must`), *how it is verified today* (test names or "none"); then the **planned** requirements from the interview, marked `planned`; non-goals; risks; success and kill criteria going forward. Requirement IDs are stable from now on.
7. **`PLAN.md`** (`full`): only the **remaining** work: slices for the roadmap the user named, plus the test gaps and debt items they chose to address, with observable acceptance criteria. Done work is not planned; it is referenced as history. Check that the slices are vertical and sized.

### 4. Adoption report

Write `ADOPTION.md` in staging, from the template: the inventory summary, the documents produced with their confidence, **what was verified versus inferred**, the **open questions**, the findings (technical debt, risks, missing tests, hotspots), and the suggested next steps.

### 5. Cross-check

Before promoting, check your own drafts: every component in the code appears in `ARCHITECTURE.md`; every implemented requirement points to code or tests; commands in `CLAUDE.md` really exist; no claim lacks a source. Offer the `coherence-checker` and `devils-advocate` agents for an independent review of the drafts. Fix what they find.

### 6. Promote

When the user accepts (document by document, or all), place the drafts in their final locations: root documents at the project root, ADRs in `docs/adr/` with their index, `ADOPTION.md` in `docs/`. Never overwrite: if a target exists, show a diff and merge only what is accepted. Keep the reconstructed banners and confidence marks until items are confirmed. Do not commit unless asked.

With `--promote`, do only this step, from the drafts already in `.trellis/adopt/`.

### 7. Hand off

Update `STATUS.md`. Tell the user plainly that the documents are **hypotheses to review**, and how to confirm items (edit the document and remove the mark). Suggest next steps by value: `security-reviewer`, `test-strategy` or the baseline gaps, then `slice` for the remaining work (if no `PLAN.md` yet) and `deliver` or `autopilot`. Do not start them unprompted.

## What not to do

- Do not change code, tests or configuration.
- Do not present a guess as a fact, or invent why a decision was made.
- Do not skip the confidence marks or the "reconstructed" banner to make the documents look authoritative.
- Do not plan work that is already done.
- Do not document every file; describe the structure that matters.
- Do not overwrite existing documents, and do not promote drafts the user has not seen.
