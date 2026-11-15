# <Project name>: design

| | |
| --- | --- |
| Status | draft / accepted |
| Version | 0.1 |
| Date | YYYY-MM-DD |
| Based on | `SPEC.md`, `ARCHITECTURE.md` |
| Interface language | ... |

# Part 1: UX

## Users and context

| User | Job to be done | Context (where, device, time, attention) | Frequency |
| --- | --- | --- | --- |
| | | | |

## UX goals

Testable statements, in priority order.

1. ...

## Journeys

| ID | Journey | Trigger | Steps | Outcome | Main failure path | Requirements |
| --- | --- | --- | --- | --- | --- | --- |
| J1 | First run | | | | | |

## Information structure

- Concepts and their names (one name per thing): ...
- Relationships: ...
- Navigation model: ...

## Screens

| ID | Screen | Purpose | Entry points | Primary action | States covered | Requirements |
| --- | --- | --- | --- | --- | --- | --- |
| S1 | | | | | empty, loading, error, offline, first use | |

### Wireframes

```
+-----------------------------+
| Title                       |
|-----------------------------|
| content                     |
|                             |
| [ Primary action ]          |
+-----------------------------+
```

## Interface text

Tone: ...

| Where | Text |
| --- | --- |
| Empty state of S1 | |
| Error: ... | |

## Walk-through

Steps counted per Must journey, friction found, and what was changed.

## Open questions (UX)

- ...

# Part 2: UI

*To do. Completed by the `ui` skill once the platform is known.*

## Platform and conventions

Target platform(s) and UI framework: ...

Conventions that apply: ...

## Identity

Three words: ... Constraints: ...

## Tokens

### Color

| Role | Light | Dark | Contrast (text on it) |
| --- | --- | --- | --- |
| background | | | |
| surface | | | |
| text-primary | | | |
| accent | | | |
| danger | | | |

### Typography, spacing, shape, motion

| Token | Value | Notes |
| --- | --- | --- |
| | | |

## Components

| Component | Native equivalent or reason for custom | Variants | States |
| --- | --- | --- | --- |
| | | | default, hover or focus, pressed, disabled, loading, error |

## Accessibility rules

- Minimum target size: ...
- Text scaling: ...
- Screen reader labels and reading order: ...
- Focus order and keyboard access: ...
- Reduced motion: ...
- Color is never the only signal: ...
- Localisation and layout direction: ...

## Adaptive layout

Sizes, orientations and devices in scope, and how the layout responds.

## Open points (UI)

- ...

## Change log

| Date | Change |
| --- | --- |
