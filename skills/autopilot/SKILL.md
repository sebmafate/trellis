---
name: autopilot
description: Run a small agent team that delivers slices of a project's PLAN.md end to end: a product-owner proposes the slice, a task-writer describes it atomically, a developer codes and tests it on a dedicated branch, a QA (verifier) checks it and sends findings back to the developer until it passes, optionally a pr-reviewer reviews the diff like a human reviewer would (`--review`), and the branch is pushed and one pull request per slice is opened. Decisions needed along the way are put to the user, then the work resumes. Use when the user wants slices built autonomously, in parallel or hands-off, with pull requests as the result ("autopilot", "run the team on the next slice", "deliver SL3 and SL4 and open PRs"). For interactive, step-by-step delivery use `deliver`. Never merges.
---

# Autopilot

Orchestrate a team of agents that takes **one slice at a time (or several in parallel)** from the plan to an open pull request. You are the orchestrator, running in the user's session: only you can talk to the user, so every question to the user goes through you.

```
product-owner ─► task-writer ─► developer ◄──► QA (verifier) ─► push + one PR per slice
       │              │             │               │
       └──────── decision needed? ──┴───────────────┘   you ask the user, then resume
```

With `--review`, a review sits between QA and the push, and a comment can follow the pull request:

```
... ─► QA accepts ─► pr-reviewer ◄──► developer (+ QA re-test) ─► push + PR ─► review comment
                     (reads the diff)    (if changes requested)               (--review-comment, or asked)
```

## Arguments

Parse what the user gave, in plain words or flags. Anything missing is asked **once**, together, at the start.

| Option | Meaning | Default |
| --- | --- | --- |
| slices (`SL3 SL4` or `next`) | Which slices to run; `next` lets the product-owner choose | `next` |
| `--push` | Authorise pushing the slice branches and opening pull requests at the end of this run | ask |
| `--parallel N` | Maximum slices in flight at once | 1 |
| `--rounds N` | Maximum QA rounds per slice before escalating to the user | 3 |
| `--base BRANCH` | Base branch for slices that do not stack on another | default branch |
| `--draft` | Open the pull requests as drafts | no |
| `--review` | Add a pull request review by the `pr-reviewer` agent after QA and before pushing | off |
| `--review-rounds N` | Fix rounds after a review that requests changes (`0` = report only, never loop) | 1 |
| `--review-comment` | Authorise posting each review as **one comment** on its pull request, without asking again | off: asked after the pull requests are open |

`--review-rounds` and `--review-comment` only matter with `--review` (passing either one switches the review on).

## Principles

- **You orchestrate; agents do the work.** You do not write the code, the brief or the QA verdict yourself.
- **The user decides, you ask.** Agents cannot ask questions; they return a status and a decision request. You put it to the user, record the answer, and resume.
- **Independence of QA.** The developer never judges its own work. The `verifier` has no edit tools and works from the brief and the criteria.
- **Bounded loops.** At most `--rounds` QA rounds per slice, then you stop that slice and tell the user. No infinite retry.
- **Nothing outward without authorisation.** Pushing and opening pull requests need the user's explicit authorisation for this run (`--push` or a yes at the start). **Posting a review as a comment is a separate outward action**: it needs `--review-comment` or the user's yes once the review is read. **Merging is never done by you**, nor force-pushing, nor pushing to the default branch. The reviewer only reports: you alone post.
- **A second, different pair of eyes.** The `pr-reviewer` reads the real diff and is not given the developer's or the QA's reports. Its verdict is advisory: it never approves or blocks anything on the forge.
- **State on disk.** Keep the run's state in files so an interrupted run can resume and nothing lives only in the chat.
- **Soft gates.** Flag a missing prerequisite and its risk, then let the user decide.
- **Honest reporting.** Say what passed, what failed, what was not verified, and what is still open.

## Steps

### 0. Preconditions

Check, and report gaps without blocking unless the run is impossible:

- `PLAN.md` exists with slices that have acceptance criteria; the project's `CLAUDE.md` gives build and test commands. If not, offer `slice` and `bootstrap`.
- It is a git repository with a remote (`git remote get-url origin`). Without one, the run can still produce local branches; say that pull requests are not possible.
- The **forge** and its CLI: from the remote URL, GitHub (`gh`) or another host with a Forgejo or Gitea CLI (`fj`; check `fj auth list`). If no usable CLI is authenticated, branches will be pushed but the pull request must be opened by the user.
- The working tree is clean enough: unrelated uncommitted changes are noted and left alone. You never touch the user's main checkout.
- Exclude run files from git without editing tracked files: ensure `.trellis/` is listed in `.git/info/exclude`. The run state, briefs, reports and worktrees live under `.trellis/` (not under `.claude/`, which Claude Code protects from writes).
- An unfinished run in `.trellis/runs/`: offer to resume it from its `state.md`.

### 1. Run options

Ask once for whatever the arguments did not settle: slices, `--parallel`, `--rounds`, base branch, draft or not, **whether to review the pull requests** (`--review`), and the **authorisation to push and open pull requests**. When asking, state exactly what that authorisation covers: the remote (show its URL), "push one new branch per slice and open one pull request per slice, never merge, never force-push".

If the review is on, say how comments work: by default nothing is posted; once the pull requests are open you will be shown each review and asked which to post as a comment. With `--review-comment` (or a yes now) each review is posted as one comment on its pull request without a further question. Posting needs a pull request, so it also needs the push authorisation.

Create `.trellis/runs/<run-id>/state.md` (create the folders if needed) (run id: date and a short word) with the options and a table of slices (status, branch, worktree, base, QA round, review verdict and round, agent IDs, PR link, review comment posted or not) and a "Decisions" section.

### 2. Product owner

Launch the `product-owner` agent (`trellis:product-owner`) with the plan, the options and the parallelism limit. Read its `STATUS:` line:

- `proposal`: present the proposed slices, their order, base branches (stacked or default) and the reasons; ask the user to confirm or adjust. If the user named slices explicitly, still run the product-owner to check readiness and bases, and show any warning.
- `nothing-ready`: report why and stop.
- `needs-decision`: handle as in step 5.

### 3. Prepare each slice

For each confirmed slice, in dependency order:

1. **Worktree and branch**: create them yourself so the base is exactly right: `git worktree add -b slice/<ID>-<short-title> .trellis/worktrees/<ID> <base>`, where `<base>` is the default branch, or the **previous slice's branch** when stacking. Refuse to reuse an existing branch without asking.
2. **Task writer**: launch `trellis:task-writer` with the slice, the base branch and the worktree path. Save its brief to `.trellis/runs/<run-id>/<ID>/brief.md`.
   - `ready`: continue.
   - `needs-decision`: step 5, then re-run or resume the task writer with the answer.
   - `blocked`: tell the user what would unblock it, mark the slice `blocked`, and move on to the others.

### 4. Develop, check, fix

For each slice that is `ready` (several at once up to `--parallel`; launch independent slices' agents in the same message so they run concurrently):

1. **Developer**: launch `trellis:developer` with the brief, the worktree path and the branch. Save its report to `<ID>/dev-report-1.md`.
   - `done`: go to QA.
   - `needs-decision`: step 5, then resume the same developer with the answer.
   - `blocked`: tell the user, mark the slice `blocked`, continue with the others.
2. **QA**: launch `trellis:verifier` with the worktree path, the brief, the plan's criteria and the developer's report. Save the report to `<ID>/qa-report-N.md`. Read the `VERDICT:` line:
   - `accept` or `accept-with-notes`: the slice is ready to publish. Keep the notes for the pull request.
   - `reject`: send the QA's numbered findings to the same developer (resume it) in **fix mode**, then run the QA again with the previous findings. Repeat up to `--rounds` rounds.
   - `needs-decision`: step 5.
3. **Rounds exhausted**: stop that slice, keep its branch local and unpushed, summarise the remaining findings for the user and ask how to proceed (more rounds, take over with `deliver`, drop the slice, or push as a draft with the findings listed).

Update `state.md` after every step.

### 5. Decisions

When any agent returns `needs-decision` (or `blocked` with a decision inside):

1. Gather the **decision requests**: the question, the options with consequences, the agent's recommendation, and whether it is architectural. If several slices are waiting, batch up to four questions in one ask and let independent slices keep moving meanwhile.
2. **Ask the user**, with the recommended option first. If you cannot ask interactively, write the questions to `state.md`, stop cleanly, and tell the user to answer and re-run `/trellis:autopilot`, which resumes from the state.
3. **Record the answer.** If it is architectural or hard to reverse, record it as an ADR with the `adr` skill's method, in the slice's worktree and branch, and commit it there so it lands in the pull request. Otherwise add it to the brief's decisions and to the pull request.
4. **Resume the agent** that asked, with the answer, using `SendMessage` to its agent ID so it keeps its context. If it cannot be resumed, launch a fresh agent with the brief, the previous report and the answer.

### 6. Final gate

For each slice that passed QA, before publishing, check it yourself in its worktree:

- Run the project's test command (and lint and build when they exist) and note the result.
- `git status` clean; `git log <base>..HEAD` shows the expected commits; `git diff --stat <base>...HEAD` shows only files that belong to the slice. Flag generated files, very large files or binaries.
- A quick scan of the diff for obvious secrets (private keys, tokens, credentials). If you find any, stop that slice and tell the user; never print the secret.

### 7. Review (only with `--review`)

For each slice that passed the final gate, **before** anything is pushed, so that fixes land in the same pull request and a local-only run is reviewed too.

1. **Draft the description.** Write the pull request description now (see step 8.3) into `.trellis/runs/<run-id>/<ID>/pr.md`, so the reviewer can check it against the diff. Refresh it after any fix round.
2. **Reviewer**: launch `trellis:pr-reviewer` with the worktree path, the base branch, the slice and its criteria, the brief and the draft description. Do **not** pass the developer's or the QA's reports. Save its report to `<ID>/review-N.md` and, from its "Comment for the pull request" section, `<ID>/review-comment.md` (rewritten at each round: it must describe the final state). Read the `REVIEW:` line:
   - `approve` or `comments`: the slice is ready to publish. Keep the remarks for the pull request description (section "Review").
   - `changes-requested`: if rounds remain (`--review-rounds`, default 1), send the reviewer's numbered findings to the same developer (resume it) in **fix mode**, then run the QA (`verifier`, re-test mode) on the new state, re-run the final gate (step 6), and re-run the reviewer with its previous findings. A QA rejection at this point is an ordinary QA round of step 4 (bounded by `--rounds`, counting the rounds already used). With `--review-rounds 0` or rounds exhausted, go to 3.
   - `needs-decision`: step 5, then re-run or resume the reviewer with the answer.
3. **Findings left open** (rounds exhausted or `--review-rounds 0`, and the verdict is still `changes-requested`): do not decide for the user. Show the remaining Blocking findings and ask: another round, take over with `deliver`, drop the slice, or push it as a **draft** with the findings listed in the description. In a non-interactive session, keep the branch local, unpushed, record the findings in `state.md` and say so in the report.

Update `state.md` after every step.

### 8. Publish

Only if the user authorised it. For each slice, in dependency order:

1. Check the remote branch does not already exist (`git ls-remote --heads origin <branch>`); if it does, stop and ask.
2. `git push -u origin <branch>` from the worktree. Never `--force`.
3. Write the pull request description from `templates/` (`PR.en.md` or `PR.fr.md`, relative to this skill's directory) into `.trellis/runs/<run-id>/<ID>/pr.md`, filled from the brief, the developer's and QA's reports and the decisions (refresh the draft written in step 7 when there is one). Fill the "Review" section from the reviewer's last verdict and remaining remarks, or delete it when the review is off. Follow the attribution rules in force for pull request descriptions in this session.
4. Open **one pull request per slice** with the forge CLI. Base: the slice's base branch (the default branch, or the previous slice's branch when stacked). Title: `<ID>: <slice title>`, prefixed `WIP: ` when drafts were asked on a Forgejo or Gitea host.
   - Forgejo or Gitea: `fj -H <host> pr create "<title>" --base <base> --head <branch> --body-file <pr.md>`
   - GitHub: `gh pr create --base <base> --head <branch> --title "<title>" --body-file <pr.md>` (`--draft` for drafts)
   - If no CLI is usable, give the user the branch name, the target and the description file instead.
5. Record the pull request link in `state.md`.

For stacked slices, say in each pull request which one it depends on and that they must be merged **in order**.

**Review comments** (only with `--review`, and only for slices whose pull request is now open). The reviewer never posts; you do, once, after all the pull requests are open:

- With `--review-comment` (or a yes given at the start): post each `<ID>/review-comment.md` as **one comment** on its pull request, without asking again.
  - Forgejo or Gitea: `fj -H <host> pr comment <number> --body-file <review-comment.md>`
  - GitHub: `gh pr comment <number> --body-file <review-comment.md>`
- Otherwise: show the user, for each slice, the verdict and the full text of the comment, and ask once which ones to post (all, none, or a list), recommending to post. Post only what the user accepts.
- In a non-interactive session you cannot ask: post nothing, keep the files, and give the user the exact command for each comment in the final report.
- Before posting, reread the text: no secret, no personal data, no local path. Follow the attribution rules in force for pull request text in this session. Never post a review, an approval or a "request changes" through the forge's review feature: a single plain comment only.
- Record in `state.md` which comments were posted. If posting fails (no usable CLI, no permission), say so and give the file and the command instead of retrying blindly.

### 9. Final report

Give the user a short, scannable summary:

- A table: slice, result (published, local only, blocked, dropped), QA rounds, review verdict and rounds (with `--review`), pull request link, review comment (posted, not posted).
- Decisions taken and ADRs written; decisions still open.
- What was not verified, and any QA or review notes to look at; Blocking review findings still open.
- **Merge order** (important when stacked) and the cleanup commands to run after merging: `git worktree remove <path>` and `git branch -d <branch>` for each slice.
- What remains in the plan, and the suggested next run.

Do not merge anything, and do not start another run unprompted.

## What not to do

- Do not push, open pull requests or merge without the authorisation given for this run; never merge, never force-push, never push to the default branch.
- Do not post a review comment without `--review-comment` or the user's yes, and never post it through the forge's approve or request-changes feature.
- Do not let the developer be its own judge, or loop beyond `--rounds` and `--review-rounds`.
- Do not give the reviewer the developer's or the QA's reports, and do not let it edit or post anything.
- Do not guess an answer to a decision that belongs to the user.
- Do not run slices in parallel when they touch the same files or component, or when one depends on another that is not done; stack or serialise them.
- Do not touch the user's main working tree.
- Do not hide a failed QA, a skipped check or an unresolved decision in the summary.
