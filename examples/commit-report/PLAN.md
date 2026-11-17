# commit-report: plan

> Illustrative example.

| Slice | Title | Serves | Size | Depends on | Status |
| --- | --- | --- | --- | --- | --- |
| SL0 | Walking skeleton: command runs on a fixture repository, CI green | all | S | none | done |
| SL1 | Month report grouped by day and project | R1, R2 | M | SL0 | done |
| SL2 | Exclude merges and other authors | R3 | S | SL1 | in review |
| SL3 | Config file mapping repositories to clients | R5 | S | SL1 | todo |
| SL4 | CSV output | R4 | S | SL1 | todo |
| SL5 | Guard test: no network | R6 | S | SL1 | todo |

Coverage: every Must (R1, R2, R3, R6) is carried by a slice.

## SL2: Exclude merges and other authors

Serves R3. Depends on SL1.

Acceptance criteria:

1. Given a repository with 3 commits by the user, 1 by someone else and 1 merge commit, the report lists exactly the 3 commits.
2. With `--all-authors`, the report lists the 4 non-merge commits.
3. The author is taken from the repository's `user.email`; if it is not set, the command stops with a clear message.

Out of scope: configuring several author emails (a later decision).
