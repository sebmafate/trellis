# <Project name>: working notes for Claude

<One sentence: what this project is.>

## Documents

| File | What it is | Rule |
| --- | --- | --- |
| `SPEC.md` | What and why: requirements `R1..`, scope, risks, kill criteria | Requirement IDs are stable; never renumber |
| `ARCHITECTURE.md` | How: components, decisions index, traceability | Update when reality changes |
| `docs/adr/` | One decision per file | Never edit an accepted ADR; supersede it |
| `DESIGN.md` | UX journeys and screens, UI tokens and components | Skip if no interface |
| `TESTING.md` | Test levels, verification matrix, definition of done | Follow it for every slice |
| `PLAN.md` | Slices `SL0..` with acceptance criteria | One slice at a time |
| `STATUS.md` | Current phase, next action | Keep current after each slice |
| `CHANGELOG.md` | User-facing changes | One line per delivered slice |

## Commands

```sh
# build
...
# test
...
# lint / format
...
# run
...
```

## Conventions

- Stack: ... (see ADR ...).
- Branches and commits: ...
- Tests come first; a slice is done only when its acceptance criteria are verified and the documents are updated (see `TESTING.md`).
- No personal data or secrets in the repository.

## Working here with Trellis

- Continue the project: `/trellis-flow:resume`, then `/trellis-flow:deliver`.
- A requirement, decision or design changes: `/trellis-flow:change-course`.
- A choice worth recording: `/trellis-flow:adr`. An unknown to settle: `/trellis-flow:spike`.
