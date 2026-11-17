VERDICT: accept-with-notes

**Verdict:** Accept with notes. All 3 criteria are verified; one test is weaker than it should be.

| Criterion | Result | Evidence |
| --- | --- | --- |
| 1. 3 user commits + 1 other + 1 merge gives exactly 3 | verified | `test_report_excludes_merge_and_other_author` passes; it fails if the filter is removed (checked by reading and by reverting the filter locally) |
| 2. `--all-authors` gives the 4 non-merge commits | verified | `test_all_authors_flag` passes |
| 3. Missing `user.email` stops with a clear message | verified | `test_missing_email_exits_2` passes; message matches DESIGN.md |

**Important**
- `tests/test_cli.py::test_missing_email_exits_2` asserts the exit code but only that the message is non-empty. Assert the text from DESIGN.md.

**Not verified:** behaviour on Windows (not a target).

**Commands run:** `python -m pytest -q` (24 passed), `ruff check` (clean).
