---
name: slice
description: Break a project's spec, architecture, design and test strategy into a PLAN.md of small vertical slices that are each deliverable, demonstrable and testable, with acceptance criteria and traceability to requirement IDs. Use after architecture (and UX/UI and test strategy) and before building, when the user asks to plan the work, split the spec into steps, make a backlog or roadmap, or re-plan after a change. Not for implementing a slice (that is `deliver`).
---

# Slice

Cut the work into **vertical slices**: each one delivers something a person can observe, can be tested on its own, and is small enough to finish in one sitting up to a few days. Output: `PLAN.md`.

## Principles

- **Vertical, not horizontal.** "Build the database layer" is not a slice. "A user can capture a text idea and see it in the list" is. Each slice cuts through every layer it needs.
- **Observable and testable.** Every slice has acceptance criteria written as observable results, taken from the verification matrix in `TESTING.md` (or from the spec's "how to verify" when there is none).
- **Small.** If a slice cannot be finished in a few days, split it. Say what size you assume (S / M / L), not hours.
- **Risk first, then value.** Put the riskiest assumptions and spikes early; then deliver the highest-value Musts. Do not postpone the scary part to the end.
- **Walking skeleton first.** Slice 0 is the thinnest end-to-end path that builds, runs, tests and (if relevant) deploys, with a passing test. Everything else grows from it.
- **Rolling wave.** Detail the next three to five slices; keep the later ones coarse and refine them as you learn.
- **Traceability.** Every **Must** requirement is covered by at least one slice. Every slice cites the requirements it serves (and the journeys and screens from `DESIGN.md`). A slice with no requirement is a smell.
- **Could requirements stay in the backlog**, not in the plan.
- **Soft gates.** Flag a missing prerequisite and what it risks, then let the user decide.

## Steps

### 0. Read the context

Read `SPEC.md` (requirements and priorities), `ARCHITECTURE.md` and the ADRs (components, spikes, open points), `DESIGN.md` (journeys, screens, states) and `TESTING.md` (verification matrix, definition of done), plus `STATUS.md` and any existing `PLAN.md` (then this is a **re-slice**: keep done slices and their IDs, never renumber).

Flag without blocking:

- No architecture: slices will be guesses at the structure; offer `architect`.
- No `TESTING.md`: derive acceptance criteria from the spec and flag that the verification strategy is undecided; offer `test-strategy`.
- Unresolved spikes on the critical path: schedule them as the first slices, or as `spike` work.
- Requirements with no way to verify: list them; the slice cannot have honest acceptance criteria.

Document language: the project's. Template in `templates/` (`.en` / `.fr`), relative to this skill's directory.

### 1. Find the thin end-to-end path

Identify the smallest flow that touches every layer the architecture has: that is slice 0, the walking skeleton. If the project is already scaffolded (`bootstrap`), slice 0 may only add the first real behavior.

### 2. Draft the slices

Work top-down from the Must requirements and the critical journeys. For each slice (`SL0`, `SL1`, ...):

- **Goal**: the outcome, in the user's words.
- **Requirements, journeys, screens** it serves.
- **Components** it touches.
- **Acceptance criteria**: observable checks (a short checklist or given / when / then).
- **Tests**: which levels from `TESTING.md` apply.
- **Depends on**: other slices, spikes, decisions.
- **Size**: S / M / L. Split any L.
- **Status**: todo.

### 3. Order

Order by dependency, then risk, then value. Show the reasoning in one line per slice when it is not obvious. Mark the milestone where the first usable version exists ("first useful slice") and what is deliberately after it.

### 4. Check coverage

Build the coverage table: every Must maps to at least one slice; every slice maps to at least one requirement or a stated technical enabler (and say so). Look at the states in `DESIGN.md`: error, empty and offline states must land in a slice too, or be consciously deferred. Report gaps plainly.

### 5. Write `PLAN.md` and hand off

From the template. Update `STATUS.md` (phase `development`, next action = the first slice). If the `devils-advocate` agent is available, offer a review of the plan: sizes, ordering, missing slices. Say the next step: `deliver` for the first slice. Do not start it unprompted.

## Re-slicing after a change

When requirements or architecture changed (see `change-course`): keep IDs of done and in-progress slices, mark obsolete slices as `dropped` with the reason, add new ones with new IDs, and recheck coverage.

## What not to do

- Do not plan horizontally by layer or by technical task.
- Do not write acceptance criteria like "works correctly" or "is fast".
- Do not detail slices far in the future; they will change.
- Do not leave a Must uncovered, or an error state forgotten.
- Do not estimate in hours with false precision.
