---
name: architect
description: 'Design a project''s architecture from its spec and produce ARCHITECTURE.md plus one ADR per significant decision. Walks through the choices that matter (platform, language, storage, hosting, auth, testing, release) one at a time, compares realistic options against the spec''s requirements, recommends, and records each decision. Use after incubation, when the user asks "what''s the right architecture / platform / language / stack", or wants an ARCHITECTURE.md with clear decisions. Also use to revisit the architecture when requirements change. For an existing project whose decisions are already in the code, use `adopt` to reconstruct them first.'
---

# Architect

Turn `SPEC.md` into `ARCHITECTURE.md` and a set of ADRs in `docs/adr/`. The goal is **clear decisions with their reasons**, not a big diagram. The smallest design that meets the Must requirements wins.

## Principles

- **Decisions come from the spec.** Every choice is justified by requirement IDs (`R1`...) and constraints. A choice that serves no requirement is suspect.
- **Smallest design that works.** Design for the Must requirements. Do not design for the Could ones, nor for scale nobody asked for. Boring, well-known technology by default; novelty must pay for itself.
- **One decision at a time.** Present options, recommend, let the user decide, record it, move on.
- **Real options, honest trade-offs.** Two to four options, including the simplest and a boring one. Compare on this project's drivers, not on generic pros and cons.
- **Uncertain? Say so and propose a spike**, a small time-boxed experiment, instead of pretending to know.
- **Do not re-ask what you know.** Skills, existing tools, platform and preferences are often in the spec, the repository or the memory you were given. Use them and confirm briefly.
- **Soft gates.** Flag a missing prerequisite and its risk, then let the user choose.
- **Size follows the project.** A small tool gets a half-page architecture and two or three ADRs.

## Steps

### 0. Read the context

Read `SPEC.md`, `STATUS.md`, and any existing `ARCHITECTURE.md`, `docs/adr/` and code. If `ARCHITECTURE.md` exists, this is an *update*: keep accepted ADRs, and supersede rather than rewrite.

Check the prerequisites and flag, without blocking:

- No `SPEC.md`, or status not `validated`: warn that decisions rest on guesses, and offer `incubate` first. If the user continues, list the assumptions you are making and put them in `ARCHITECTURE.md` under open points.
- Verdict "test first" with an untested riskiest assumption: remind the user that this assumption may invalidate the design.
- Requirements without a way to verify them: note it; it will hurt later slicing and testing.

Document language: the project's, as for the other documents; templates are in `templates/` (`.en` / `.fr`), relative to this skill's directory.

### 1. Set the decision drivers

From the spec, extract the **Must** requirements and the constraints. Then ask which **quality attributes** matter here, from this list, proposing a ranking: privacy, offline use, performance, reliability, security, cost to run, maintainability by one person, portability, time to first version. Keep the **top three to five** as decision drivers; everything else is secondary. Write them down; options will be judged against them.

### 2. List the decisions to make

Propose the list of decisions this project actually needs, ordered by dependency and risk, for example: platform and distribution, language and runtime, data storage and sync, hosting or deployment, authentication and authorisation, major dependencies, testing approach, build and release, observability. Drop what does not apply. Mark each as:

- **ADR**: hard to reverse or surprising.
- **Note**: obvious or cheap to change; one line in `ARCHITECTURE.md` is enough.

Let the user add, remove or reorder. Start with the decision that constrains the others, or the riskiest one.

### 3. Decide, one at a time

For each ADR-worthy decision:

1. State the question and which requirements and drivers bear on it.
2. Present two to four options in a compact table judged on the drivers (qualitative marks like better / neutral / worse are fine; avoid fake numeric scores).
3. **Recommend one**, with the reason in a sentence or two, and say what you are least sure about.
4. If a key fact is unknown, propose a **spike**: what to try, the time box, and what result would decide (the `spike` skill frames, runs and records it). Record the decision as `proposed` until the spike is done.
5. When the user decides, record it using the `adr` skill's method (file, numbering, index). Do not move on with an undecided item silently; mark it open.

When the user is stuck, offer the default you would take and why.

### 4. Assemble `ARCHITECTURE.md`

From the template. Only what is decided; anything not decided is listed as open, never papered over. Contents: context and scope, decision drivers, overview with a small Mermaid diagram, components and their responsibilities, data and storage, key flows, cross-cutting concerns (security and privacy, errors, observability, testing, build and release, cost), the **decisions index** linking each ADR, the **traceability table**, risks and spikes, deferred items.

### 5. Check coverage

Before declaring done, check the traceability table yourself:

- Every **Must** requirement maps to at least one component and, where relevant, an ADR.
- Every component maps to a requirement or a driver. Flag components that exist "just in case".
- Every open point has an owner or a next step.

Report gaps plainly. If the `devils-advocate` agent is available, offer an independent review of the result.

### 6. Hand off

Update `STATUS.md`: phase `architecture`, next action, date. Say what comes next, in this order, skipping what does not apply:

1. `ux`, if the project has an interface and no `DESIGN.md` yet (it can also be done before this step), then `ui` once the platform is decided.
2. `test-strategy`, to turn the "Testing" notes and each requirement's "how to verify" into a `TESTING.md`.
3. `bootstrap`, to scaffold the repository from these decisions (the project's `CLAUDE.md`, a walking skeleton).
4. `slice`, to cut the work into deliverable, testable pieces in `PLAN.md`; then `deliver` builds them one by one.

Do not start any of them unprompted.

## What not to do

- Do not pick a stack before the drivers are written down.
- Do not produce a diagram-only architecture without decisions and reasons.
- Do not decide for the user. Recommend, then record what *they* decided.
- Do not add components, services or layers to look thorough.
- Do not edit accepted ADRs; supersede them.
