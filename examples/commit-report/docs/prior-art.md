# commit-report: prior art

| | |
| --- | --- |
| Date checked | 2026-03-03 |
| Based on | `SPEC.md` 1.0 |

> Illustrative example. The candidates below are **generic categories**, not real products, so there are no links or dates to check. A real run lists named tools with links and the date each was checked.

## Question

Does something already turn a month of git history into a client-ready activity report, offline?

## Criteria

| # | Criterion | From |
| --- | --- | --- |
| C1 | Works offline, nothing sent anywhere | R6 |
| C2 | Several repositories in one report | R1 |
| C3 | Grouped by day and project, readable without editing | R2 |
| C4 | Skips merges and other authors | R3 |
| C5 | Nothing to host or pay for | constraints |

## Candidates

| Candidate | Kind | Covers | Fit for this project |
| --- | --- | --- | --- |
| `git log` with a format string and a loop | platform feature | C1, C2 partly, C4 with flags | Does about half; no grouping per day and project |
| A hosted time tracker with git integration | commercial service | C3 | Sends the history to a third party (fails C1), and costs money (fails C5) |
| A changelog generator | open source tool | C3 partly | Built for releases, not for a month of mixed work |

## Options

| Option | For | Against | Cost to build or maintain |
| --- | --- | --- | --- |
| Adopt the `git log` loop | Free, now | Manual grouping each month | None |
| Adapt a changelog generator | Existing code | Wrong model (releases) | Medium, plus adapting |
| Build | Exactly the shape needed | A small tool to maintain | Low if kept small |
| Do nothing | Free | One hour lost every month | None |

## Verdict

**Adopt the `git log` loop first, as the cheapest test, and build only if the raw output is a usable base.** If the loop already saves enough time, do nothing more. Building stays small by design: one command, no dependency.

## Consequences for the spec

- Riskiest assumption A1 and the cheapest test come from this survey. Kill criterion added: more than 20 minutes of editing.

## Sources

None (illustrative).
