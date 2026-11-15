---
name: change-course
description: Handle a change in a running project consistently: a new, modified or dropped requirement, a pivot, a technology change, a design change or a scope cut. It analyses the impact across SPEC, ADRs and ARCHITECTURE, DESIGN, TESTING, PLAN and the code, sizes the change, gets the user's decision, then updates every document in the right order without renumbering or rewriting history. Use when the user says "we need to change X", "drop this feature", "add this requirement", "let's switch to Y", or when a spike or review invalidates something.
---

# Change course

Projects change. The danger is not the change but the **documents drifting apart** after it. This skill makes a change once, everywhere, in order, with the reasons recorded.

## Principles

- **Impact before action.** Find everything a change touches before editing anything, and show it to the user.
- **Order matters.** Update in dependency order: `SPEC.md` → ADRs and `ARCHITECTURE.md` → `DESIGN.md` → `TESTING.md` → `PLAN.md` → `STATUS.md`.
- **History is kept.** Never renumber requirements, slices or ADRs. A dropped requirement is marked `dropped` with the date and reason. A changed decision is a new ADR that supersedes the old one. A changed requirement keeps its ID and gets a note in the decision log.
- **Size the change honestly.** Small (a clarification), medium (a requirement or component changes, some slices redone), large (the problem, the platform or the riskiest assumption changes). A large change may mean the idea needs `incubate` again or a `retro`.
- **The user decides.** Present the impact and the options, recommend one, and proceed only on agreement.
- **Check the escape hatches.** Does the change trigger a kill criterion, invalidate the riskiest assumption, or hit an ADR's "revisit when" condition? Say so.
- **Soft gates.** Flag a missing prerequisite (no spec, no plan) and its risk, then let the user decide.

## Steps

### 1. Capture the change

In one or two sentences: what changes, why now (the trigger: a spike result, a review, user feedback, a constraint), and what the user wants to happen. Ask one question at a time for what is missing. If the change is only a clarification of an existing requirement, say so and treat it as small.

### 2. Read the context

Read `SPEC.md`, the ADRs and `ARCHITECTURE.md`, `DESIGN.md`, `TESTING.md`, `PLAN.md`, `STATUS.md`, and look at the code that relates to the change.

### 3. Impact analysis

Build a table of what the change touches:

| Where | What it affects | Action |
| --- | --- | --- |
| `SPEC.md` | requirements added, changed, dropped; scope; kill criteria | |
| ADRs / `ARCHITECTURE.md` | decisions to supersede, components, traceability | |
| `DESIGN.md` | journeys, screens, states, tokens | |
| `TESTING.md` | verification matrix rows | |
| `PLAN.md` | slices to drop, redo, add; done slices affected | |
| Code | modules or tests to change or remove | |

Add the **cost** in plain terms (slices to redo, work thrown away, risks introduced) and the **cost of not changing**.

### 4. Present options and decide

Offer two or three options when there are real ones (for example "change it now", "defer to after the first useful slice", "do not change"), recommend one with the reason, and ask the user to decide. If the user chooses to proceed against a gap or a risk you flagged, log that.

### 5. Apply, in order

1. **`SPEC.md`**: edit requirements (IDs kept), mark dropped ones, update scope and risks, add a line to the decision log.
2. **ADRs and `ARCHITECTURE.md`**: write new ADRs that supersede old ones through the `adr` skill; update components, the decisions index, the traceability table and the change log.
3. **`DESIGN.md`**: update journeys, screens and states, and the UI part if affected.
4. **`TESTING.md`**: update the verification matrix.
5. **`PLAN.md`**: drop obsolete slices (keep them, marked `dropped`), add new ones with new IDs, re-check coverage (as in `slice`), and note rework for done slices.
6. **`STATUS.md`**: phase, next action, risks.
7. **Code and tests**: do not change them here unless the change is trivial and the user asks; plan the work as slices and use `deliver`.

### 6. Check and report

Re-check coverage and consistency: every Must still has a slice and a verification, no document mentions a dropped requirement as live, ADR statuses match. If the `coherence-checker` agent is available, offer it. Summarize what changed in each document, what is now open, and the next action.

## What not to do

- Do not edit documents before the impact is shown and agreed.
- Do not renumber IDs or delete history.
- Do not rewrite an accepted ADR.
- Do not change code as part of the document update.
- Do not hide that a change invalidates the project's premise; say it.
