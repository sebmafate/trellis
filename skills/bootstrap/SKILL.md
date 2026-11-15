---
name: bootstrap
description: Scaffold a new project from its decisions: create the project's CLAUDE.md that tells Claude where the SPEC, ARCHITECTURE, DESIGN, TESTING, PLAN and ADRs are and how to work, the docs layout, .gitignore, a README, CHANGELOG, CI skeleton and a runnable hello-world with a passing test, using the stack chosen in the ADRs. Use after architecture and test strategy, before the first slice, when the user wants to set up, scaffold, initialise or start the project repository. Uses the stack's official generators rather than hand-written boilerplate.
---

# Bootstrap

Set up the repository so that the first slice can start immediately and Claude knows how the project works. Output: a **walking skeleton** (it builds, runs and passes one test) plus the project's own `CLAUDE.md` and the document layout.

## Principles

- **Decisions come from the ADRs.** Language, framework, build tool, test framework, CI: take them from `ARCHITECTURE.md` and the ADRs. If the stack is not decided, stop and offer `architect`; do not pick one here.
- **Official generators first.** Use the stack's own tooling (`swift package init`, `cargo new`, `npm create`, `dotnet new`, `uv init`...) over hand-written boilerplate. Check the current command; do not recall flags from memory when they can be looked up.
- **Minimal.** Scaffold what the first slices need, not every folder the framework could have.
- **It must run.** The skeleton is finished only when the build command and the test command both succeed. Say what you ran.
- **The project's `CLAUDE.md` is short and factual**: where things are, how to build and test, rules to follow. It is not a copy of the spec.
- **Outward actions need agreement.** Creating the local repository is fine; creating a remote repository, pushing, or enabling CI services is the user's decision.
- **Soft gates.** Flag a missing prerequisite and its risk, then let the user decide.

## Steps

### 0. Read the context

Read `SPEC.md`, `ARCHITECTURE.md`, the ADRs, `TESTING.md` (test tooling, quality gates), `DESIGN.md` (UI platform), and look at what is already in the folder. Never overwrite existing files; merge or ask.

Flag without blocking:

- No architecture or no stack decision: offer `architect`.
- No `TESTING.md`: the test harness choice is open; offer `test-strategy`, or pick the stack's default and note it.
- A non-empty folder with its own structure: adapt to it.

Document language: the project's. Templates are in `templates/` (`.en` / `.fr`), relative to this skill's directory.

### 1. Confirm the plan

List what you will create, with the commands you will run: the generator command, the repository layout, the CI target (if any), the lint and format tools. Get the user's agreement before writing.

### 2. Create the skeleton

- Run the stack's generator for the application or library structure, with the names and versions from the ADRs.
- Create `docs/` with `docs/adr/` (and `docs/spikes/` if spikes exist). Keep `SPEC.md`, `ARCHITECTURE.md`, `DESIGN.md`, `TESTING.md`, `PLAN.md`, `STATUS.md` where the other skills put them (project root), or move them only if the user asks.
- Add a `.gitignore` for the stack (caches, build outputs, dependency folders, editor files), including `.trellis/`, where the `autopilot` skill keeps its isolated worktrees and run state. Add a `README.md` stub (what it is, how to build and run, link to the docs), and a `CHANGELOG.md` with an "Unreleased" section.
- Add the lint and format configuration the stack decisions call for, if any.
- Initialise git if needed. Make **no** first commit unless the user agrees.

### 3. Write the project's `CLAUDE.md`

From the template: the map of documents and what each one is for, the build, test, lint and run commands (the real ones, verified), the conventions that follow from the ADRs and `TESTING.md`, the rules (do not edit accepted ADRs, keep `STATUS.md` and `PLAN.md` current, requirement IDs are stable), and how to use the Trellis skills here. Keep it under a page.

### 4. Walking skeleton

Make the thinnest end-to-end path real: one tiny behavior, one test that passes, executed through the build and test commands. If `PLAN.md` has a `SL0`, align with it.

### 5. CI skeleton (if wanted)

If the testing strategy or ADRs call for CI, add the workflow file that runs build and tests on each commit, matching the quality gates in `TESTING.md`. Note which service it targets (Forgejo Actions, GitHub Actions...). Do not push it or enable runners without agreement.

### 6. Verify and hand off

Run the build and the test commands, and report the results. Update `STATUS.md` (phase `development`, next action). Suggest `slice` if there is no `PLAN.md`, otherwise `deliver` for the first slice. Do not start it unprompted.

## What not to do

- Do not choose or change the stack.
- Do not leave the skeleton unbuilt or its test unrun.
- Do not overwrite existing files.
- Do not create remotes, push, or enable services without agreement.
- Do not stuff the project's `CLAUDE.md` with the spec.
