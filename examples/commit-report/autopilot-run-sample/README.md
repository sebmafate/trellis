# What an `autopilot` run leaves behind

> Illustrative example, written by hand. In a real project these files live in `.trellis/runs/<run>/` and `.trellis/followups/`, which are **excluded from git**; they are shown here so you can see what each agent produces. The command was:
>
> ```
> /trellis:autopilot SL2 --push --review
> ```
>
> then, after a human review and a failing check on the pull request:
>
> ```
> /trellis:follow-up 3 --push --reply
> ```

| File | Written by | What it is |
| --- | --- | --- |
| [`state.md`](state.md) | `autopilot` | The run's options and the table of slices |
| [`SL2/brief.md`](SL2/brief.md) | `task-writer` | The slice cut into atomic tasks |
| [`SL2/qa-report-1.md`](SL2/qa-report-1.md) | `verifier` | The QA verdict, with evidence per criterion |
| [`SL2/review-1.md`](SL2/review-1.md) | `pr-reviewer` | The diff review, with its comment for the pull request |
| [`SL2/pr.md`](SL2/pr.md) | `autopilot` | The pull request description |
| [`follow-up-pr-3/feedback.md`](follow-up-pr-3/feedback.md) | `follow-up` | The raw feedback collected from the pull request |
| [`follow-up-pr-3/reply.md`](follow-up-pr-3/reply.md) | `follow-up` | The summary comment posted after the fixes |
