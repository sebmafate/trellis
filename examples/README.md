# Examples

*Français : [README.fr.md](README.fr.md)*

## `commit-report/`: the documents of a small project, phase by phase

A **fictional** project (a command-line tool that turns a month of git history into an activity report) shown as Trellis would leave it partway through development. The documents were **written by hand** to show their shape and how they refer to each other; they are not a transcript of a real run, and the project does not exist. Nothing here contains real products, links or dates to rely on.

Read it in this order, each file being the output of one phase:

| Phase | Skill | File |
| --- | --- | --- |
| Incubation | `incubate` | [SPEC.md](commit-report/SPEC.md) |
| Incubation (optional) | `prior-art` | [docs/prior-art.md](commit-report/docs/prior-art.md) |
| Design | `architect`, `adr` | [ARCHITECTURE.md](commit-report/ARCHITECTURE.md), [docs/adr/](commit-report/docs/adr/README.md) |
| Design | `ux` (a command line is an interface too) | [DESIGN.md](commit-report/DESIGN.md) |
| Design | `test-strategy` | [TESTING.md](commit-report/TESTING.md) |
| Development | `slice` | [PLAN.md](commit-report/PLAN.md) |
| Any time | `deliver`, `resume` | [STATUS.md](commit-report/STATUS.md), [CHANGELOG.md](commit-report/CHANGELOG.md) |
| Development (optional) | `autopilot --review`, `follow-up` | [autopilot-run-sample/](commit-report/autopilot-run-sample/README.md) |

What to notice:

- **Requirement IDs** (`R1`...) run through the spec, the architecture's traceability table, the test matrix and the plan's acceptance criteria. A requirement without a check, or a Must without a slice, would be flagged.
- **The riskiest assumption** (A1) and its **cheapest test** and **kill criteria** are written before any code.
- **Each decision** has its own record with the options that were *not* chosen.
- **`STATUS.md`** ends with one next action, so a pause costs nothing.

`naming` is not shown: its value is in real search results, and made-up ones would be misleading.
