---
name: test-strategy
description: 'Define how a project will be tested and verified, and write it as TESTING.md: the risks that matter, the mix of test levels, a verification matrix that maps every requirement to a check, test data and environments, quality gates, non-functional and manual testing, and the definition of done. Use after architecture (and UX/UI when there is an interface), before slicing the work, or when the user asks "how should we test this", "what tests do we need", or wants a test plan. Not for writing the tests themselves; that happens during delivery.'
---

# Test strategy

Decide **what to verify, at which level, how, and when**, before code exists, so that every requirement has a way of being proved and testing effort goes where the risk is. Output: `TESTING.md`.

## Principles

- **Derive tests from requirements.** Every Must requirement ends up in the verification matrix with a concrete check. A requirement nobody can verify is a defect in the spec; say so.
- **Risk first.** Spend test effort where a failure is costly or likely: data loss, privacy, money, sync conflicts, concurrency, parsing, anything that learns or guesses. Trivial code gets little.
- **Proportional.** Default to many fast unit tests, fewer integration tests, very few end-to-end tests, then justify any departure. A weekend tool needs a short plan.
- **Deterministic.** Control time, randomness, network and concurrency. A flaky test is treated as a defect, not tolerated.
- **Coverage is not the goal.** Do not set a coverage percentage as the target. Use it, at most, to find untested areas.
- **Testability is design feedback.** If a component is hard to test, that is an architecture finding. Report it so `architect` can address it, instead of working around it.
- **No personal data in the repository.** Fixtures are synthetic or anonymised. If real data is needed to judge quality (a classifier, say), it stays outside the repo, and the plan says how it is used.
- **Soft gates.** Flag a missing prerequisite and its risk, then let the user choose.

## Steps

### 0. Read the context

Read `SPEC.md` (requirements, "how to verify", risks, success criteria), `ARCHITECTURE.md` and the ADRs (components, stack, the testing note, spikes), and `DESIGN.md` if there is an interface. Read an existing `TESTING.md` if present (then this is an *update*) and any existing tests.

Flag without blocking:

- No architecture: the plan can only be generic; offer `architect` first, or write a spec-level plan and mark the tooling as open.
- Requirements with an empty or vague "how to verify": list them; they will need a decision.

Document language: the project's. Templates are in `templates/` (`.en` / `.fr`), relative to this skill's directory.

### 1. Risks and goals

List the few things that would hurt most if they broke or were wrong, from the spec's risks and the architecture's components, and the things that are hard to test (machine learning quality, hardware, time, external services, graphics). Confirm the ranking with the user. State the testing goals in a sentence or two (for example "fast feedback while coding; confidence before a release").

### 2. Levels

Choose the mix and fill the table: for each level (unit, integration, contract, end to end, UI, property-based, snapshot, performance, security, accessibility, manual and exploratory), say what it covers, which tool the stack decisions imply, where it runs (local, CI), and how often. Drop levels that do not apply, and explain the ones you drop that a reader might expect.

### 3. Verification matrix

For every requirement: priority, the level at which it is verified, the concrete check (an observable result, not "works"), and whether it is automated. Add the **success criteria** from the spec that can only be measured after release or in daily use (dogfooding), with how they will be observed.

### 4. Test data and environments

Fixtures and factories, synthetic versus real data and where each lives, doubles for external services, how time, randomness and concurrency are controlled, environments (local, CI, a device or simulator, staging if any). Cover migrations, sync conflicts and recovery when the architecture has them.

### 5. Quality gates

What runs on each commit, on each pull request, and before each release. What **blocks** and what only **warns** (keep it soft: a gate that is too strict gets bypassed). A policy for flaky tests. How a regression in a measured quality (accuracy, performance budget) is handled, noting that a gate cannot rely on data that is not available where it runs.

### 6. Non-functional and manual testing

Performance budgets taken from the architecture's decision drivers, security and privacy checks, accessibility checks from the `ui` rules, and what stays manual or exploratory with a short checklist. Beta or dogfooding exit criteria tie back to the spec's success criteria.

### 7. Definition of done

Write the checklist a delivery slice must satisfy to count as done: tests written and passing, acceptance criteria verified, documentation and status updated, no new warnings. `slice` and `deliver` use it.

### 8. Tooling decisions

Record the test tooling choices. Hard-to-reverse ones (a test framework tied to the language, a UI test strategy) get an ADR through the `adr` skill; the rest are one-line notes.

### 9. Check and hand off

Check that every Must has a verification, every high-risk component has tests at a sensible level, and every open point has a next step. Report gaps plainly, and any testability problem found in the architecture. Update `STATUS.md`. Suggest `bootstrap` next if the repository is not scaffolded yet, then `slice`. Do not start either unprompted.

## What not to do

- Do not write the test code here.
- Do not demand every level for a small project.
- Do not set coverage targets as the goal.
- Do not plan tests that need data you cannot legally or safely keep.
