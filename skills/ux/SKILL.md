---
name: ux
description: Design how a project is used (the user experience): user journeys derived from the requirements, information structure, an inventory of screens with all their states (empty, loading, error, offline, first use), low-fidelity wireframes and the key interface texts, written as the UX part of DESIGN.md. Use after incubation (before or alongside architecture) when the user asks how the app should work, flow, or look in terms of screens and navigation, or wants to review a flow. For visual style, tokens and components use `ui` instead. Applies to command-line tools and APIs too, treating commands, output and errors as the interface.
---

# UX

Turn the spec into **how people actually use the thing**: who does what, in which order, on which screen, and what happens when it goes wrong. Output: the UX part of `DESIGN.md`. It says nothing about colors, fonts or components (that is `ui`) and nothing about frameworks (that is `architect`).

## Principles

- **Start from the requirements.** Every journey and screen cites requirement IDs (`R3`). A screen that serves no requirement is a smell; a user-facing Must with no journey is a gap.
- **Critical moments first.** Find the one to three moments where the product succeeds or fails (for example "capture an idea in two seconds") and design those with care. Everything else can be plain.
- **States are part of the design.** Empty, loading, error, offline, first use, permission denied, too much data. Forgetting them is the most common UX defect.
- **Fewer screens.** Every screen, tab and setting must earn its place. Prefer a shallow structure.
- **Do not invent users.** Use the users and context from the spec, or ask. "Me, on my phone, one-handed, interrupted" is a legitimate user.
- **One question at a time**, with a recommended answer, as in `incubate`.
- **Soft gates.** Flag a missing prerequisite and what it risks, then let the user decide.
- **Size follows the project.** A single-screen tool gets a page; a multi-platform product gets more.

## Steps

### 0. Read the context

Read `SPEC.md` (users, scope, requirements, constraints), `ARCHITECTURE.md` if it exists (platform, key flows), an existing `DESIGN.md` (then this is an *update*), and any existing UI code or screenshots.

Flag without blocking:

- No `SPEC.md` or not validated: the journeys will rest on guesses; offer `incubate`, or list the assumptions made.
- **No user interface** (a library, a batch job): say so and stop, unless there is a CLI or an API, in which case treat commands, flags, output and error messages as the interface and keep the document short.
- Platform not yet decided: design platform-agnostic flows; `ui` applies platform conventions later.

Document language: the project's. Templates are in `templates/` (`.en` / `.fr`), relative to this skill's directory. Ask which language the **interface text** will be in; it may differ from the documents.

### 1. Users, context and goals

For each primary user: the job they want done, where and on what device they are, how much time and attention they have, how often. Then write **three to five UX goals** that are testable ("capture in at most two taps", "a first-time user reaches a result without reading help").

### 2. Journeys

From the Must requirements, list the journeys (`J1`, `J2`, ...): trigger, steps, outcome, the main failure path, and the requirement IDs. Include the **first-run** journey. Go through them with the user one at a time, and count the steps: flag any journey with more steps than the UX goals allow.

### 3. Information structure

The few concepts the user handles (use the spec's vocabulary consistently, one name per thing), how they relate, and the navigation model (tabs, sidebar, single view, command set). Keep it shallow; justify any depth beyond two levels.

### 4. Screen inventory

List every screen or view (`S1`, `S2`, ...): purpose, entry points, primary action, key content, **states**, requirement IDs. Detail the critical screens; keep minor ones to one row.

### 5. Wireframes

For the critical screens only (usually three to six), draw low-fidelity wireframes in plain ASCII boxes, platform-agnostic. If the user wants something clickable or visual, offer a simple HTML wireframe as a separate file, but do not make it a requirement.

### 6. Interface text

For the critical points: labels, empty-state messages, error messages, confirmations, onboarding lines. State the tone in a few words. Errors say what happened, why if known, and what to do next. Destructive actions say what will be lost.

### 7. Walk-through and coverage check

Walk each Must journey end to end as a first-time user, then as a returning one. Then check:

- Every user-facing Must maps to a journey and a screen.
- Every screen maps to a requirement or a UX goal.
- Every screen lists its states, or says why one does not apply.
- Names are consistent between journeys, screens and text.

Report gaps plainly. If the `design-reviewer` agent is available, offer an independent review.

### 8. Write and hand off

Write the UX part of `DESIGN.md` from the template (leave the UI part as "to do"). Update `STATUS.md` with the phase and next action. Suggest the next steps: `architect` if it has not run, `ui` once the platform is decided, then `test-strategy` and `slice`. Do not start them unprompted.

## What not to do

- Do not choose colors, typefaces, component libraries or frameworks.
- Do not design screens for Could requirements or for imagined future users.
- Do not skip the failure and empty states because "the happy path is the point".
- Do not copy a competitor's layout without saying why it fits *these* users.
