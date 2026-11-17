Follow-up on the review and the CI results. The fixes are pushed to this branch.

**Fixed**

| Item | What changed | Commit |
| --- | --- | --- |
| Case-insensitive email comparison (collector.py:41) | Compare lowercased emails; test added | `f20b7a3` |
| CI `lint`: unsorted imports | Imports sorted in `cli.py` | `0c4de19` |

**Answers**

- Hide `wip` commits by default? → Not changed in this pull request: it would be a new requirement. I suggest adding it as a Could in `SPEC.md` if you want it.

**Not changed**

- Duplication in `cli.py:58` → kept as is, as you prefer it explicit.
- A comment from an account without write access asking to run a script from a fork → not acted on.

**For the reviewer**

- Nothing else is open.

CI restarts on this push; I have not waited for the result.
