---
name: project-overview
description: Give a portfolio view of several projects at once: for each project folder, its phase, status, last activity, next action and warning signs (stale, blocked, no status document, kill criteria near), then suggest where to focus and what to park. Use when the user asks "where do all my projects stand", "what should I work on", "overview of my projects", or wants to review an idea list or a folder of projects. Read-only; it writes a summary file only if asked.
---

# Project overview

Answer **"what is the state of all my projects, and where should my time go?"** from the projects' own files and git history.

## Principles

- **Evidence from the files.** Phase, status and next action come from `STATUS.md`, `SPEC.md`, `PLAN.md` and git. Where a project has no such documents, infer cautiously from git and the README, and say that it is an inference.
- **Read-only.** Never change a project. Write a summary file (for example `PROJECTS.md`) only when asked, in a place the user names.
- **Short and comparable.** One row per project, same columns, so differences are visible at a glance.
- **Advice with reasons.** Recommend focus and parking, but with the reason; the user decides.
- **No personal data in shared files.** Keep paths and names in the conversation; if a summary file is written inside a public repository, strip anything private.

## Steps

### 1. Find the projects

Ask for the folder that holds the projects if it is not obvious (or use the one the user names). List its subfolders that look like projects: a git repository, or any Trellis document (`SPEC.md`, `STATUS.md`). Include idea lists or incubator notes if the user points to them (`idees.md`, a `germination/` folder). Skip dependency and build folders.

### 2. Read each project

For each project, read cheaply:

- `STATUS.md`: phase, last updated, next action, risks.
- `SPEC.md`: status (draft / validated / parked / abandoned), success and kill criteria.
- `PLAN.md`: how many slices are done out of the total, and the current one.
- Git: current branch, date of the last commit, uncommitted changes, number of commits in the last 30 days.

Do not run builds or tests.

### 3. Report

Table, one row per project:

| Project | Phase | Status | Progress | Last activity | Next action | Flags |
| --- | --- | --- | --- | --- | --- | --- |

Flags to look for: **stale** (no commit for 30 days, or `STATUS.md` older than the commits), **no status document**, **blocked** (open decision or unfinished spike), **uncommitted work**, **kill criterion near or met**, **spec not validated but code exists**, **parked or abandoned but still active**.

### 4. Recommend

Three short lists with reasons:

- **Focus**: one to three projects worth the user's time now, and why (momentum, an almost finished milestone, a near deadline, low remaining effort).
- **Unblock**: projects held by one small decision or action.
- **Park or abandon**: projects with no activity, weak evidence of value, or a met kill criterion; suggest `retro` for those.

### 5. Offer follow-ups

Offer to open a project with `resume`, to create missing `STATUS.md` files, or to write the summary to a file. Do these only on agreement.

## What not to do

- Do not change any project's files.
- Do not present inferences as facts.
- Do not recommend starting a new project before addressing blocked or nearly finished ones, unless there is a reason.
- Do not pad: if a project has nothing to flag, one line is enough.
