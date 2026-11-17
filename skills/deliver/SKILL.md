---
name: deliver
description: 'Build the next slice of a project''s PLAN.md, one slice at a time: write the tests for its acceptance criteria first, implement the minimum to pass, verify (tests, build, independent verifier), update PLAN.md, STATUS.md, CHANGELOG.md and the other documents, and commit. Use when the user wants to implement, build, code or ship the next step or a named slice (for example "deliver SL3"), once a PLAN.md exists. Reports deviations instead of hiding them.'
---

# Deliver

Take **one slice** from `PLAN.md` to done: tested, verified, documented, committed. Small steps, honest reporting, documents kept in step with the code.

**Autonomous alternative:** for well-specified slices, to run several in parallel, or to get pull requests without supervising, the `autopilot` skill runs a team of agents (product owner, task writer, developer, QA) and opens one pull request per slice. Use this skill instead when you want to steer step by step, or when the slice is uncertain, sets a pattern, or involves decisions you expect to make on the way. See the usage guide for the comparison.

## Principles

- **One slice at a time.** Finish and report before starting another, unless the user asked for several ("deliver the next three"); then still do them one by one.
- **Tests first.** Turn the slice's acceptance criteria into failing tests, see them fail for the right reason, then make them pass.
- **Minimum to pass.** Implement what the criteria need, following `ARCHITECTURE.md` and `DESIGN.md`. No scope creep: new ideas go to the plan's backlog, not into this slice.
- **Never weaken a test to make it pass.** If a criterion is wrong, say so and change it deliberately, with the user.
- **Deviations are reported, not hidden.** If the code needs something the architecture or design does not allow, stop and surface it: adjust the code, or open a decision (`adr`, `change-course`). Do not drift silently.
- **Documents are part of done.** A slice with stale `STATUS.md` or `PLAN.md` is not done.
- **Honest outcomes.** If tests fail, say so with the output. If something was not verified, say so.
- **Soft gates.** Flag a missing prerequisite (no acceptance criteria, no tests setup) and its risk, then let the user decide.

## Steps

### 0. Read and choose

Read `PLAN.md`, `STATUS.md`, the project's `CLAUDE.md` (build, test and run commands, branch and commit conventions), and the parts of `ARCHITECTURE.md`, `DESIGN.md` and `TESTING.md` the slice touches.

Choose the slice: the one the user named, otherwise the first `todo` whose dependencies are done. Check the working tree: uncommitted changes unrelated to the slice should be noted and not mixed in.

Ask **once**, and remember for the session: should each slice end with a commit, and on a branch of its own? Default suggestion: one branch `slice/SLn-short-title`, one commit at the end. Follow the project's own convention if `CLAUDE.md` states one. Do not commit without the user's agreement.

### 1. Restate

Say in a few lines: the slice goal, its acceptance criteria, the files and components you expect to touch, and anything unclear. If the criteria are vague or untestable, fix them with the user now. Mark the slice `in progress` in `PLAN.md` and `STATUS.md`.

### 2. Tests first

Write the tests for each acceptance criterion at the levels `TESTING.md` prescribes. Run them and confirm they **fail**, and fail because the behavior is missing, not because of a typo. For UI work, include the states from `DESIGN.md`.

### 3. Implement

Make the tests pass with the smallest coherent change, respecting the architecture's component boundaries and the design's tokens and components. Commit-sized steps; run the tests as you go. If you hit a decision that deserves an ADR, pause and record it through the `adr` skill.

### 4. Verify

- Run the slice's tests, then the whole test suite, the linter and the build, using the project's commands.
- For user-facing work, exercise it for real (run the app or the command) and look at the result, not only at the tests.
- For a non-trivial slice, offer to call the `verifier` agent for an independent check against the acceptance criteria, and do so by default when the user has not declined.

Fix what the checks find. If something cannot be verified here, say what and why.

### 5. Update the documents

- `PLAN.md`: slice status `done`, criteria ticked, notes on what differed.
- `STATUS.md`: phase, last updated, next action (the next slice).
- `CHANGELOG.md`: one user-facing line under "Unreleased" (Added / Changed / Fixed), citing the slice.
- `TESTING.md`: the verification matrix rows now automated.
- `ARCHITECTURE.md`, `DESIGN.md`, README: only if reality differed from what they say; record decisions through `adr` rather than editing accepted ones.

### 6. Commit

If agreed: one commit for the slice, message in the imperative, mentioning the slice ID and the requirement IDs. Stage deliberately: check `git status` and add only the files that belong to the slice, never generated files (caches, compiled files, build outputs, dependency folders); if the project lacks an ignore rule for them, say so rather than committing them. Do not push unless asked.

### 7. Report

Say in a short summary: what is now true for the user, what was verified and how, what deviated or was deferred, anything left open, and the next slice. Then stop.

## What not to do

- Do not start the next slice automatically.
- Do not edit `SPEC.md`, accepted ADRs or acceptance criteria on your own to make the work easier.
- Do not mark a slice done with failing, skipped or disabled tests.
- Do not hide a failure or a workaround in a summary.
- Do not mix unrelated changes into the slice's commit.
