---
name: ui
description: Define a project's visual and interaction system once the platform is known: platform conventions to follow, a minimal set of semantic design tokens (color for light and dark, type, spacing, shape, motion), the reusable components with their states, and accessibility rules, written as the UI part of DESIGN.md. Use after `ux` and `architect`, when the user asks about visual style, design tokens, theming, dark mode, components, or accessibility of the interface. Prefers native platform components over custom ones.
---

# UI

Give the project a **small, coherent visual system** that fits its platform and is accessible by construction. Output: the UI part of `DESIGN.md`. It builds on the screens and states from `ux` and the platform decided in `architect`.

## Principles

- **System first.** Use the platform's native components, conventions and settings (text scaling, dark mode, reduced motion, system colors) by default. Build a custom component only where a screen needs something the platform does not offer, and say why.
- **Small and semantic.** A handful of tokens named by *role* (`background`, `text-primary`, `accent`, `danger`), never by value (`green-500`). Fewer tokens that are actually used beat a large unused palette.
- **Both themes from the start.** Every color token has a light and a dark value.
- **Accessibility is part of the system**, not a later pass: contrast, target size, text scaling, screen-reader labels, focus order, reduced motion, no meaning carried by color alone.
- **Measure, do not eyeball.** Contrast ratios are computed (a short script is fine) and written down. Platform guideline claims you are unsure about are checked against the current guidelines, or flagged as unverified.
- **One question at a time**, with a recommended answer. Do not ask what the spec, the architecture or the code already tells you.
- **Soft gates.** Flag a missing prerequisite and its risk, then let the user choose.
- **Size follows the project.** A small tool may need only tokens and three components.

## Steps

### 0. Read the context

Read `DESIGN.md` (the UX part: screens, states, journeys), `ARCHITECTURE.md` (platform and UI framework decisions), `SPEC.md` (constraints, existing brand) and any existing UI code.

Flag without blocking:

- No UX part: the component list would be guessed; offer `ux` first.
- Platform not decided: offer to run `architect` for that decision, or produce platform-agnostic tokens and mark the platform notes as open.
- No UI at all: say so and stop.

Document language: the project's. The template is shared with the `ux` skill: `../ux/templates/DESIGN.en.md` or `DESIGN.fr.md`, relative to this skill's directory. This skill completes the UI part of that file.

### 1. Platform conventions

Name the target platform(s) and UI framework, and list the conventions that apply to this project: navigation patterns, standard controls, gestures, keyboard and pointer behavior, window or size classes, system integration. If you are unsure of a current guideline, look it up rather than recall it.

### 2. Identity

Ask for the desired feel in three words, and any constraint (existing brand, colors, an app family it must match). Keep it modest: for most projects the identity is "native, calm, readable".

### 3. Tokens

Propose, and agree with the user, a minimal set:

- **Color**: semantic roles with a light and a dark value each. Text and essential UI must meet WCAG AA contrast (4.5:1 for normal text, 3:1 for large text and UI components). Compute and record the ratios.
- **Typography**: a short scale, using the platform's text styles where they exist so text scaling works.
- **Spacing**: a base unit and a few steps.
- **Shape and elevation**: corner radii, borders, shadows.
- **Motion**: durations and easing, with a reduced-motion fallback.

### 4. Components

From the screen inventory, list the reusable components (button, list row, input, empty state, banner...). For each: the native component it maps to, or why it is custom, its variants, and its states (default, hover or focus, pressed, disabled, loading, error). Make sure every screen can be built from the list.

### 5. Accessibility rules

Write the project's checklist: minimum target size, text scaling behavior, screen-reader labels and reading order, focus order and keyboard access, reduced motion, color not being the only signal, and, where relevant, localisation and right-to-left layout.

### 6. Adaptive layout

How the layout behaves across window sizes, orientations and devices the project targets. Keep to what is in scope.

### 7. Write and check

Complete the UI part of `DESIGN.md`. Then verify:

- Every screen from `ux` uses only the listed components and tokens.
- Contrast ratios are recorded for each text/background pairing, in both themes.
- Every custom component has a justification.

If the `design-reviewer` agent is available, offer an independent review.

### 8. Hand off

Update `STATUS.md`. Suggest `test-strategy` (accessibility and visual checks belong in it) and `slice`. Where tokens live in code is decided during delivery, not here.

## What not to do

- Do not produce a full design system for a one-screen tool.
- Do not name tokens after their values.
- Do not override platform behavior (text size, dark mode, motion settings) for looks.
- Do not claim a guideline from memory when it can be checked.
