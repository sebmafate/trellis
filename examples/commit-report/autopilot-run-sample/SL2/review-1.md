REVIEW: comments

**Verdict:** Comments. Nothing blocking; one real inconsistency and two small points.

**Important**
1. `src/commit_report/collector.py:41`: the author filter compares `user.email` case-sensitively. Git treats the local part as case-sensitive but many users type it differently across machines, so a commit can be dropped silently. The comparison should ignore case, with a test.

**Suggestions**
- `src/commit_report/cli.py:58`: `--all-authors` and the default share a long branch; a small helper would read better.

**Looks good:** the fixture helper builds commits with real `git`, which keeps the tests honest.

**Not checked:** performance on a very large history.

## Comment for the pull request

Automated review by the Trellis `pr-reviewer` agent. Advisory: it does not replace a human review.

Verdict: comments, nothing blocking.

Important
- `src/commit_report/collector.py:41`: the author comparison is case-sensitive, so a commit made with a differently-cased email is dropped silently. Compare case-insensitively and add a test.

Suggestion
- `src/commit_report/cli.py:58`: the default and `--all-authors` branches could share a helper.
