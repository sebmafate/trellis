---
name: resume
description: Pick a project back up after a pause: read STATUS.md, PLAN.md, the git history and the working tree, then say where the project stands, what is in progress, what is stale or inconsistent, and exactly what to do next. Use when the user returns to a project, asks "where were we", "what's next" or "what's the state of this project", or starts a session in a project folder and needs to get oriented. Read-mostly; it only updates STATUS.md if the user agrees.
---

# Resume

Give the user an accurate, short answer to **"where are we and what do I do next?"**, based on the files and the repository, not on anyone's memory.

## Principles

- **Evidence over recall.** Everything in the report comes from files, git or tests you actually looked at. Say when the status document and the repository disagree.
- **Short.** One screen. The user is trying to get moving, not to read a report.
- **One recommended next action**, concrete enough to run (for example "`/trellis:deliver SL3`").
- **Read-mostly.** Do not change code or documents. Offer to refresh `STATUS.md` and do it only if the user agrees.
- **Works with partial documents.** Use what exists; say what is missing and what that costs.

## Steps

### 1. Gather

Read, as available: `STATUS.md`, `PLAN.md`, `SPEC.md` (status, success and kill criteria), `ARCHITECTURE.md` and `docs/adr/` (proposed decisions), `docs/spikes/` (unfinished spikes), `CHANGELOG.md`, the project's `CLAUDE.md`, and `README`.

Then look at the repository: current branch, `git status` (uncommitted work), the last 10 to 15 commits with dates, other branches with unmerged work, and test or build state if it is cheap to check (do not run long suites without asking).

### 2. Cross-check

Compare the documents with git and note any mismatch:

- `STATUS.md` older than the latest commits.
- A slice marked `in progress` with no recent commits, or `done` with no matching commits.
- Uncommitted changes that belong to no slice.
- Accepted decisions that the code contradicts (surface it; the `coherence-checker` agent does the deep check).
- A `proposed` ADR or an unfinished spike blocking a slice.

### 3. Report

Use this shape, skipping empty parts:

1. **Phase and last activity** (one line, with dates).
2. **Done**: the slices or milestones completed, briefly.
3. **In progress**: the slice, the branch, uncommitted changes.
4. **Open and risky**: blockers, proposed decisions, unfinished spikes, stale documents, mismatches found.
5. **Next action**: one recommendation, with the exact command or step, and a second option if it is a real choice.

### 4. When there is nothing to go on

- No Trellis documents but an existing repository: summarize what the repository shows (what it is, last activity) and offer to start from `incubate` or `bootstrap`.
- Documents but no repository: say so and offer to create one.
- An empty folder: say so and offer `incubate`.

### 5. Offer to refresh

If `STATUS.md` is stale or missing, offer to update it (phase, last updated, next action, risks) from what you found. Write it only on agreement.

## What not to do

- Do not trust `STATUS.md` over the repository when they differ; report both.
- Do not run long builds or test suites unprompted.
- Do not change anything except `STATUS.md`, and only with agreement.
- Do not start the next action; recommend it.
