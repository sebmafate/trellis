## Summary

The report now leaves out merge commits and other people's commits by default; `--all-authors` brings the others back.

Slice **SL2: Exclude merges and other authors** of `PLAN.md`. Serves: R3.

## Changes

| Task | What changed | Commit |
| --- | --- | --- |
| T1 | Merge commits excluded in the collector | `a1c4e07` |
| T2 | Only the repository's `user.email` by default | `b92d5f1` |
| T3 | `--all-authors` option | `c03a8de` |
| T4 | Clear stop when `user.email` is missing | `d7f61b2` |

## Acceptance criteria

| Criterion | Result | Evidence |
| --- | --- | --- |
| 3 + 1 + merge gives exactly 3 | met | `test_report_excludes_merge_and_other_author` |
| `--all-authors` gives 4 | met | `test_all_authors_flag` |
| Missing `user.email` stops clearly | met | `test_missing_email_exits_2` |

## QA

Verdict: **accept with notes** after 1 round. One test asserted the exit code but not the message text; fixed in `e18b3c9`.

## Review

Automated review (`pr-reviewer`): **comments** after 1 round. Open remark: the author comparison is case-sensitive (see the comment on this pull request).

## Decisions

None.

## Deviations and not verified

- Windows behaviour not verified (not a target).

## How to review and merge

- Base: `main`.
- Cleanup after merging: `git worktree remove .trellis/worktrees/SL2` and `git branch -d slice/SL2-exclude-noise`.
