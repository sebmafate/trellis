# Work brief: SL2, exclude merges and other authors

Serves R3. Base `main`. Readiness: **ready**.

| Task | Expected result | Test to write first | Likely files |
| --- | --- | --- | --- |
| T1 | The collector returns no merge commit | Fixture with one merge: expect it absent | `collector.py`, `tests/test_collector.py` |
| T2 | The collector keeps only commits by the repository's `user.email` | Fixture with two authors: expect only the user's | same |
| T3 | `--all-authors` includes the other author (merges still excluded) | End-to-end golden file with the flag | `cli.py`, `tests/golden/` |
| T4 | If `user.email` is not set, the command stops with the message from DESIGN.md | Run on a repository without it: exit code 2 and message | `cli.py`, `tests/test_cli.py` |

Out of scope: several author emails.

Decisions needed before coding: none.
