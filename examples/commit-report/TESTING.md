# commit-report: test strategy

> Illustrative example.

## Risks that matter

1. A report that silently drops or duplicates commits (R1, R2).
2. Wrong exclusion of merges or authors (R3).
3. A network call sneaking in (R6).

## Levels

| Level | What | Tools |
| --- | --- | --- |
| Unit | grouper and renderer on in-memory records | pytest |
| Integration | collector against a fixture repository built with real `git` commands | pytest, temporary directories |
| End to end | the command on the fixture, compared with golden files | pytest |

## Verification matrix

| Requirement | Check | Level | Status |
| --- | --- | --- | --- |
| R1 | Golden file for one month, two repositories | end to end | done (SL1) |
| R2 | Golden file, order by day then project | end to end | done (SL1) |
| R3 | Fixture with a merge and two authors | integration | planned (SL2) |
| R4 | CSV header and one row | unit | planned (SL4) |
| R5 | Sample config | integration | planned (SL3) |
| R6 | Run with sockets disabled | end to end | planned (SL5) |

## Quality gates (soft)

Tests, `ruff` and a type check pass in CI before a pull request is merged. A failing gate is flagged; the user decides.

## Definition of done for a slice

Acceptance criteria met and each backed by a test that would fail if the feature broke; matrix, `PLAN.md`, `STATUS.md` and `CHANGELOG.md` updated; no skipped test.
