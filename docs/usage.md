# Using Trellis

*Français : [usage.fr.md](usage.fr.md)*

Trellis is a [Claude Code](https://claude.com/claude-code) plugin. It adds skills (and, later, agents) that guide a project from a raw idea to shipped software. This page explains how to install it and how to work with it.

## 1. Install

You need Claude Code. The commands below are **terminal commands** (`claude plugin …`), which work everywhere Claude Code is installed, including hosts such as T3 Code. In Claude Code's own interactive interface you can type the same thing as a slash command (`/plugin install …`, `/plugin marketplace update …`); where that command does not exist, use the terminal. Install the plugin from this repository's marketplace:

```
claude plugin install trellis --marketplace https://github.com/sebmafate/trellis.git
```

The shorthand `owner/repo` is accepted too:

```
claude plugin install trellis --marketplace sebmafate/trellis
```

**From a private fork or a self-hosted copy**, the HTTPS URL fails (Git cannot ask for a password in this context). Use the SSH form `git@host:owner/repo.git` instead, for example:

```
claude plugin install trellis --marketplace git@example.com:owner/trellis.git
```

Claude Code **rejects `ssh://…` URLs** ("Invalid marketplace source format"). The `git@host:path` form has no room for a port, so a non-standard SSH port must be set in `~/.ssh/config` (`Host example.com` with `Port 2222`, for instance).

The same thing in two steps, which is useful to know when you want to update it:

```
claude plugin marketplace add <repository URL>
claude plugin install trellis@trellis
claude plugin marketplace update trellis
claude plugin update trellis@trellis
claude plugin list
```

### Try it without installing

Clone the repository and point Claude Code at it for one session:

```sh
git clone <repository URL> trellis
claude --plugin-dir ./trellis
```

This is also how you test your own changes while working on the plugin.

### Check that it works

Run `claude plugin list` in a terminal: `trellis@trellis` should appear as enabled. Plugins are loaded when a session starts, so **start a new session** (a new thread in T3 Code) after installing or updating. Then type `/trellis:` where slash commands are available and look for `trellis:incubate`, or just ask Claude to incubate an idea.

## 2. The idea in one picture

Each phase writes a file in **your project's repository**; the next phase reads it. Files, not chat history, are the shared memory. You can stop for a month, come back, and nothing is lost.

```
idea ──► INCUBATION ──► DESIGN ─────────────────► DEVELOPMENT ────────► LIFE OF THE PROJECT
         SPEC.md        ARCHITECTURE.md + ADRs    PLAN.md               STATUS.md
                        DESIGN.md (UX, UI)        (deliverable slices)  CHANGELOG.md
                        TESTING.md
```

Trellis is **strict but soft**. If a prerequisite is missing (for example, no validated spec before design), the skill says what is missing and what it risks, then lets you decide. When you go ahead anyway, the override is written down in the document's decision log, so the choice stays visible later.

## 3. What is available today

| Name | Kind | Phase | Status |
| --- | --- | --- | --- |
| `incubate` | skill | incubation | **available** |
| `adopt` | skill | any (existing project) | **available** |
| `prior-art` | skill | incubation | **available** |
| `naming` | skill | incubation | **available** |
| `devils-advocate` | agent | incubation, design | **available** |
| `ux` | skill | design | **available** |
| `architect` | skill | design | **available** |
| `adr` | skill | design | **available** |
| `spike` | skill | design | **available** |
| `ui` | skill | design | **available** |
| `design-reviewer` | agent | design | **available** |
| `security-reviewer` | agent | design, release | **available** |
| `test-strategy` | skill | design | **available** |
| `bootstrap` | skill | development | **available** |
| `slice` | skill | development | **available** |
| `deliver` | skill | development | **available** |
| `autopilot` | skill | development | **available** |
| `product-owner` | agent | development | **available** |
| `task-writer` | agent | development | **available** |
| `developer` | agent | development | **available** |
| `verifier` | agent | development | **available** |
| `pr-reviewer` | agent | development | **available** |
| `publish` | skill | release | **available** |
| `resume` | skill | any | **available** |
| `change-course` | skill | any | **available** |
| `retro` | skill | any | **available** |
| `project-overview` | skill | any | **available** |
| `coherence-checker` | agent | any | **available** |
| `SessionStart` hook | hook | any | **available** |

Every item has its own section below, followed by a suggested order of use.

### Already have a project?

Trellis follows a project from its idea, but you can bring an **existing, advanced project** under it with the `adopt` skill (see section 26). It reconstructs the documents from the code, tests, CI and git history, marks everything **"reconstructed, to be confirmed"**, and never touches your code. Then the rest (`slice`, `deliver`, `autopilot`, `change-course`...) works on it.

## 4. Incubate an idea (`incubate`)

### Start

Open Claude Code **in the folder where the project will live** (an empty folder or an existing repository), then either describe your idea:

> I'd like to build a small tool that turns my commits into a monthly activity report. Can we incubate it?

or call the skill directly:

```
/trellis:incubate a small tool that turns my commits into a monthly activity report
```

Both work. The direct call is handy if Claude does not pick the skill up on its own.

### What happens

1. **Context.** Claude reads the folder (existing `SPEC.md`, README, notes) before asking anything, so it does not ask what is already written. If a `SPEC.md` exists, it updates it rather than starting over.
2. **Seed.** It restates your idea in two sentences. Correct it if it is wrong.
3. **Interview.** One question at a time, each with a suggested answer. Reply "ok" to accept it, or correct it. The topics, in order:
   problem and user, why this and why now, success criteria, scope and **non-goals**, constraints, existing solutions, riskiest assumption, cheapest test, kill criteria.
4. **Challenge.** Before writing, it tells you what worries it: scope creep, vague success criteria, an untested assumption, an existing tool that already does most of it.
5. **Draft.** It writes `SPEC.md` and a minimal `STATUS.md`.
6. **Verdict.** It ends with a recommendation:

| Verdict | Meaning |
| --- | --- |
| **Proceed** | The spec is solid; next step is design |
| **Test first** | The riskiest assumption must be checked first; the test is described |
| **Park** | Not now; what would reopen it is noted |
| **Abandon** | Not worth building, with the reason |

Abandoning or parking is a valid, useful result. It means the idea was cheap to kill.

### What you get

- **`SPEC.md`**: summary, problem, success criteria, scope (in and out), requirements, constraints, prior art, risks and assumptions, cheapest test, kill criteria, open questions, decision log.
- **`STATUS.md`**: the current phase, the next action, and where things stand.

Requirements carry stable IDs (`R1`, `R2`, ...), a priority (Must / Should / Could) and *how they will be verified*. Later phases trace against these IDs, so avoid renumbering them by hand.

### Tips

- **Short answers are fine.** "Ok", "yes", or a few words are enough; the skill proposes, you correct.
- **Say "I don't know".** Unknowns go to the open-questions or assumptions sections instead of being invented.
- **Size follows the idea.** A small personal tool gets a short interview and a one-page spec.
- **Override freely.** If the verdict is "test first" and you want to go on anyway, say so. It will be logged, not refused.
- **Come back later.** Run it again in the same folder to revise the spec; it works in update mode.
- **Existing products.** When the project is meant for **other people**, `incubate` proposes the `prior-art` survey by default (with its market angle) and logs it if you skip it. For a strictly personal tool, a quick search is enough.
- **A name.** A working name is fine. `incubate` asks once whether you want help finding a real one, and offers the `naming` skill at the end (see below).

### Find a name (`naming`)

```
/trellis:naming
```

Optional: it first asks whether you want help, and stops if you do not. It asks a short brief (what the name should evoke and not suggest, who will say and read it, languages, where it must be available, where the project will live), proposes about a dozen candidates across different directions (metaphor, coined word, evocative, other languages) with the reason and the known risk of each, and helps you cut to three to five finalists. It then **screens the finalists on the web** and records where and when: same or close names in the field, domains, repository and package names, app stores, meaning in other languages, and the public trademark registers it could really search. The verdict is "no conflict found in the places checked", "close conflict" or "blocking", never "free". Written to `docs/naming.md`; the chosen name goes into the spec's title and decision log. You can also ask it to check a name you already have. It is a first screening, not a trademark search or legal advice: for a commercial project, have the registers searched by a professional.

## 5. Design the architecture (`architect`)

Run it in the same folder, once `SPEC.md` exists:

```
/trellis:architect
```

or just ask: "What's the right architecture for this?"

1. **Context.** It reads `SPEC.md` and `STATUS.md`. If the spec is missing or not validated, it warns you of the risk and offers to incubate first; you can continue anyway, and the assumptions it makes are listed.
2. **Decision drivers.** From your Must requirements it proposes the three to five qualities that decide trade-offs (privacy, offline use, cost to run, maintainability by one person...). You adjust the ranking.
3. **The list of decisions.** Platform, language, storage, hosting, authentication, testing, release... It separates what deserves an ADR (hard to reverse) from what is a one-line note.
4. **One decision at a time.** Two to four real options, compared on *your* drivers, with a recommendation and what it is least sure about. If a key fact is unknown, it proposes a **spike** (a small time-boxed experiment) and records the decision as `proposed` until it is done. You decide; it records.
5. **`ARCHITECTURE.md`.** Context, drivers, overview with a Mermaid diagram, components, data, key flows, cross-cutting concerns, a decisions index, and a **traceability table** (requirement → components → ADRs).
6. **Coverage check.** Every Must must land on a component; every component must serve a requirement. Gaps are reported plainly.

Tips: ask for the default it would take if you are stuck; say "this is a weekend tool" to keep the output short; run it again later to revise the design (accepted ADRs are superseded, never rewritten).

## 6. Record a decision (`adr`)

`architect` uses it automatically, but you can call it on its own whenever you make a technical choice:

```
/trellis:adr we chose SQLite over Postgres because the app is single-user and offline
```

It writes `docs/adr/NNNN-short-title.md` (numbered, one decision per file) with the context, **the options considered and why the others were rejected**, the decision, the consequences (including the negative ones), and a measurable trigger to revisit it. It also keeps `docs/adr/README.md` (the index) and the decisions table of `ARCHITECTURE.md` in sync.

An accepted ADR is never edited. When a decision changes, ask for a new ADR that **supersedes** the old one: only the old one's status line changes. ADRs are never deleted or renumbered.

## 7. Get a second opinion (`devils-advocate`)

A skeptical reviewer that runs as a separate agent, so its opinion is not anchored on the conversation that produced the document. It is **read-only**: it reports, it never edits.

Ask for it in plain words, for example:

> Use the devils-advocate agent to review my SPEC.md.

> Run trellis:devils-advocate on the architecture and the ADRs.

It reads `SPEC.md`, `ARCHITECTURE.md`, `docs/adr/` and `STATUS.md`, and reports: an overall judgement, **blocking** problems, **important** ones, minor ones, the two or three questions you must answer, and what is solid. Every finding cites the file and section. It scales to the project (a weekend tool is not held to enterprise standards) and says "no blocking issue" when that is true.

Good moments to call it: right after `incubate` (before investing), and after `architect` (before slicing the work).

## 8. Design the user experience (`ux`)

For any project with an interface. Run it after `incubate`, before or alongside `architect`:

```
/trellis:ux
```

It reads `SPEC.md` and works through: the users and their context, **testable UX goals**, the **journeys** (one per Must requirement, including the first run), the information structure, the **inventory of screens with all their states** (empty, loading, error, offline, first use...), low-fidelity ASCII wireframes for the critical screens, and the key interface texts. Everything cites requirement IDs, and it ends with a walk-through of each journey and a coverage check (every user-facing Must has a screen; every screen serves a requirement).

The result is the **UX part of `DESIGN.md`**. It says nothing about colors or frameworks. For a command-line tool or an API, it treats commands, output and error messages as the interface. A project with no interface at all is told so and skipped.

## 9. Define the visual system (`ui`)

Run it once the UX part exists and the platform is decided:

```
/trellis:ui
```

It names the platform conventions to follow, proposes a **small set of semantic tokens** (color with light and dark values, type, spacing, shape, motion) with contrast ratios computed and recorded, lists the **components with their states** (preferring native ones, justifying custom ones), and writes the accessibility rules (target size, text scaling, screen-reader labels, focus order, reduced motion). The result is the **UI part of `DESIGN.md`**.

If the platform has not been decided, it flags it and offers to produce platform-agnostic tokens.

## 10. Plan the tests (`test-strategy`)

Run it after the architecture (and the design, if there is an interface), before slicing:

```
/trellis:test-strategy
```

It identifies the risks that matter, picks a proportional mix of test levels, and builds a **verification matrix**: every requirement gets a concrete, observable check at a given level. It also covers test data (no personal data in the repository), environments, **quality gates** (what blocks, what only warns), non-functional and manual testing, and a **definition of done** for each slice. The result is `TESTING.md`.

If something is hard to test because of how the system is built, it reports it as a finding to bring back to `architect`. It does not write the tests; that happens during delivery.

## 11. Get a design critique (`design-reviewer`)

The design counterpart of `devils-advocate`: an independent, **read-only** agent.

> Use the design-reviewer agent on my DESIGN.md.

It checks journeys against requirements, missing states, step counts, error messages, vocabulary consistency, and, for the UI part, platform fit, tokens, contrast, accessibility and components. It reports blocking, important and minor findings with file and section, and says "no blocking issue" when that is true. It cannot run the app, so contrast ratios it did not verify are labelled "stated, not checked".

## 12. Survey what exists (`prior-art`)

```
/trellis:prior-art
```

A deeper, sourced version of the quick check in `incubate`, proposed by default when the project is meant for other people. You agree on five to eight **criteria taken from your requirements**, then it looks at products, open-source projects, platform features and combinations of tools, evaluates each against the criteria (with links and the date checked, and vendor claims labelled as such), and ends with a verdict: **adopt, adapt, build or do nothing**. Written to `docs/prior-art.md`. "Adopt X instead" is a valid result.

For a project meant for others, it adds a short **market angle**: how the candidates position and price themselves, how users find and choose them, signs of demand and what would differentiate your project. It does not pretend to be a market study: whether people would pay or switch becomes the riskiest assumption and a cheap test.

## 13. Settle an unknown (`spike`)

```
/trellis:spike can a share extension write to the shared store in under 200 ms?
```

Frames **one question**, with success, failure and inconclusive criteria written **before** the experiment and a time box. It helps design the cheapest experiment, run it, and record the result in `docs/spikes/NNNN-title.md`, then feeds the verdict back into the ADR (a `proposed` decision becomes `accepted`, or is superseded), the architecture's risk table, the spec's kill criteria and the plan. Spike code is throwaway.

## 14. Scaffold the project (`bootstrap`)

```
/trellis:bootstrap
```

Run after architecture and test strategy. It takes the stack from your ADRs, shows its plan, then uses the stack's **official generator** to create the structure, adds `docs/`, `.gitignore`, a README stub and `CHANGELOG.md`, and writes the **project's own `CLAUDE.md`** (where each document is, the real build and test commands, the rules). It ends with a **walking skeleton** that builds and passes one test. It never overwrites existing files, and creating a remote, pushing or enabling CI waits for your agreement.

## 15. Plan the work (`slice`)

```
/trellis:slice
```

Cuts the work into `PLAN.md`: **vertical slices** (`SL0`, `SL1`...), each observable, testable and small, with acceptance criteria taken from the verification matrix, the requirements, journeys and screens it serves, a size (S / M / L) and dependencies. Slice 0 is the walking skeleton; risky things come early; every Must is covered by a slice (a coverage table checks it); error and empty states are planned too. Later slices stay coarse. Re-running it after a change keeps the IDs of done slices.

## 16. Build slices (`deliver` or `autopilot`)

```
/trellis:deliver SL1
```

or "deliver the next slice". For one slice: it restates the criteria, writes the **tests first** and watches them fail, implements the minimum to pass, runs the tests, lint and build (and exercises user-facing work for real), offers an independent check by the `verifier`, then updates `PLAN.md`, `STATUS.md`, `CHANGELOG.md` and the test matrix, and commits if you agreed. It reports deviations instead of hiding them, never weakens a test to make it pass, and stops after each slice. It asks once whether each slice should be committed on its own branch.

### Or let a team do it: `autopilot`

```
/trellis:autopilot next --push
/trellis:autopilot SL3 SL4 --parallel 2 --rounds 3 --push
```

`autopilot` runs a small **team of agents** that takes slices from the plan to an **open pull request**, one PR per slice. It runs in your session (only it can talk to you); the agents do the work:

```
product-owner ─► task-writer ─► developer ◄──► QA (verifier) ─► push + one PR per slice
       │              │             │               │
       └──────── decision needed? ──┴───────────────┘   it asks you, then resumes
```

| Role | Agent | What it does |
| --- | --- | --- |
| Product owner | `product-owner` | Proposes the next slice or slices from `PLAN.md`: readiness, priority, risk, which can run in parallel, and on which base branch. You confirm. |
| Task writer | `task-writer` | Describes the slice **atomically**: small ordered tasks, each with an observable expected result, the test to write first, likely files and what is out of scope. Says what is missing if it cannot. |
| Developer | `developer` | Codes and tests task by task on a **dedicated branch in a dedicated worktree**, one commit per task, documents updated. |
| QA | `verifier` | Checks independently against the brief and the criteria. If it rejects, its numbered findings go back to the **same developer**, who fixes, and the QA re-tests, for up to `--rounds` rounds (default 3). |
| Reviewer (optional, `--review`) | `pr-reviewer` | Reviews the real diff like a human reviewer would, before the push: bugs, readability, consistency, risk, scope, accuracy of the PR description. See "Reviewing the pull request" below. |

**Decisions.** The agents cannot ask you questions. When one needs a decision, it stops and returns a structured request (question, options, recommendation, whether it is architectural). `autopilot` asks you, records an ADR in the slice's branch if the decision is architectural, then **resumes the same agent** with your answer. Other independent slices keep moving meanwhile. In a non-interactive session, it saves the questions in the run's `state.md` and stops, with nothing built and nothing pushed; run it again with your answers in the message (for example "Q1 = a, Q2 = a, and yes you may rewrite the SL3 criterion") to resume. A vague criterion in the plan is treated the same way: it asks your permission to replace it with concrete examples, in the slice's branch.

**The end result.** For each slice that passes QA, it runs a final check itself (tests, clean tree, only the slice's files, no obvious secret), pushes the branch, and opens **one pull request** with a generated description (summary, tasks, criteria with evidence, QA verdict, decisions, deviations, how to merge). It uses `fj` for a Forgejo or Gitea host and `gh` for GitHub. **It never merges, never force-pushes and never pushes to your default branch.** Pushing and opening pull requests need the authorisation you give at the start (`--push` or a yes); without it, you get local branches only.

| Option | Meaning | Default |
| --- | --- | --- |
| `SL3 SL4` or `next` | Slices to run; `next` lets the product owner choose | `next` |
| `--push` | Authorise pushing branches and opening pull requests | asked |
| `--parallel N` | Slices in flight at once | 1 |
| `--rounds N` | QA rounds per slice before escalating to you | 3 |
| `--base BRANCH` | Base branch for slices that do not stack | default branch |
| `--draft` | Open the pull requests as drafts | no |
| `--review` | Add a review by the `pr-reviewer` agent after QA and before pushing | off |
| `--review-rounds N` | Fix rounds after a review that requests changes (`0` = report only) | 1 |
| `--review-comment` | Post each review as one comment on its pull request without asking again | off (asked once the PRs are open) |
| `--dev-model MODEL` | Model of the `developer` (`sonnet`, `opus` or `haiku`) | the agent's own (`sonnet`) |
| `--review-model MODEL` | Model of the judges, `verifier` and `pr-reviewer` | the agents' own (`opus`) |

#### Which model for which agent?

Each agent declares its model in the `model:` line of its header in `agents/<name>.md`. The defaults follow one idea: **a stronger model where the work is to judge or to specify, a cheaper one where it is to execute or to read mechanically.**

| Agent | Model | Why |
| --- | --- | --- |
| `developer` | `sonnet` | Writes and runs the most, so it is the biggest cost. |
| `verifier`, `pr-reviewer` | `opus` | They are judges: a judge stronger than the author catches more. A different model also gives a little more independence from the code's author. |
| `task-writer` | `opus` | Its brief conditions everything that follows. |
| `devils-advocate`, `security-reviewer`, `design-reviewer` | `opus` | Judgement and scepticism, called rarely. |
| `product-owner`, `coherence-checker` | `sonnet` | Reading and cross-referencing, fairly mechanical. |

`haiku` is not used by default: the saving is small next to the risk of missing a finding. To change it for one run, use `--dev-model` and `--review-model`; to change it for good, edit the `model:` line (`sonnet`, `opus`, `haiku`, or `inherit` to use your session's model). The aliases always point to the latest version of each family. If your plan does not include a model, `autopilot` falls back to the agent's own and says so in the run state; for a standalone agent, set `model: inherit`. Cost is not measured here: moving the developer to `sonnet` lowers it only if your session does not already run on a cheaper model.

#### Reviewing the pull request (`--review`)

```
/trellis:autopilot next --push --review
/trellis:autopilot SL3 SL4 --push --review --review-comment
```

The QA answers "does the slice meet its criteria?". The review answers the other question, the one a careful colleague asks before merging: "would I be comfortable owning this code?". With `--review`, once a slice has passed QA and the final check, the `pr-reviewer` agent reads the **real diff** against the base branch and reports bugs and unhandled cases, unclear or needlessly complex code, duplication, deviations from the codebase's conventions and the ADRs, risky changes (security, dependencies, migrations), scope creep, and whether the pull request description says what the diff really does. It is **not given the developer's or the QA's reports**, so it forms its own opinion. It reports; it never edits, never approves and never posts.

- **Before the push.** The review runs before the branch is pushed, so fixes land in the same pull request, and a run without `--push` is reviewed too.
- **Verdict.** `approve`, `comments` (remarks, nothing blocking) or `changes-requested` (at least one blocking finding). The verdict is advisory; nothing is blocked on the forge.
- **Fix loop.** On `changes-requested`, the numbered findings go back to the same developer, the QA re-tests and the reviewer looks again, for `--review-rounds` rounds (default 1; `0` = report only). If blocking findings remain, `autopilot` asks you: another round, take over with `deliver`, drop the slice, or push it as a draft with the findings listed. Remarks are summarised in a "Review" section of the pull request description.
- **Posting a comment is separate and needs your say-so.** By default **nothing is posted**: once the pull requests are open, you are shown each review (verdict and full text) and asked which to post, as **one plain comment** per pull request (never the forge's approve or request-changes). With `--review-comment`, or a yes at the start, each review is posted without a further question. In a non-interactive session nothing is posted; the files and the exact commands are in the final report. The comment starts with a line saying it is an automated, advisory review. Posting needs an open pull request, so it also needs the push authorisation.
- **Files.** `.trellis/runs/<run>/<ID>/review-N.md` (full report) and `review-comment.md` (the text that would be posted).

It is an extra agent run per slice (more with fix rounds), and it uses the same model as the rest of the team, so it complements a human review and does not replace it.

**Stacked slices.** A slice that depends on another that is not merged yet is built on **that slice's branch** and its pull request targets it, so you get a chain of PRs to merge **in order**. Slices that touch the same files or components are not run in parallel.

**Files it keeps.** The run state, briefs and reports live in `.trellis/runs/<run>/` and the worktrees in `.trellis/worktrees/`. The whole `.trellis/` folder is excluded from git locally (autopilot adds it to `.git/info/exclude`, which may ask for your permission; `bootstrap` puts it in `.gitignore`). An interrupted run can be resumed from its `state.md`.

You can also use the agents alone: ask the `product-owner` "what should we build next?", the `task-writer` to prepare a slice before building it by hand, the `verifier` to check any piece of work, or the `pr-reviewer` to review any branch or pull request.

### `deliver` or `autopilot`?

| | `deliver` (skill) | `autopilot` (skill running a team) |
| --- | --- | --- |
| Who works | Claude in your conversation | A team of agents: product owner, task writer, developer, QA |
| Supervision | Step by step; you see every test and edit and can steer | Hands-off between your decisions; a report and pull requests at the end |
| Questions | Asks you as they come up | Collects decisions, asks you, then resumes the agent |
| Quality control | Optional `verifier` at the end | Built in: a QA loop (up to `--rounds`) before anything is pushed, plus an optional pull request review (`--review`) |
| Your working copy | Works in it (on a slice branch if you agree) | Never touches it; worktrees and branches of its own |
| Result | A commit, if you agree | A pushed branch and **a pull request per slice** (with authorisation) |
| Parallel work | One slice at a time | Several independent slices; stacked PRs for dependent ones |
| Context | Knows the whole conversation | Each agent knows only the brief and the project files |
| Cost | One conversation | Several agents per slice and several rounds; parallel runs multiply it |

**Use `deliver` when**

- it is one of the first slices, the walking skeleton, or anything that sets a pattern the rest will follow;
- the slice is uncertain, touches the architecture, or involves visual and interaction judgement;
- the criteria are not crisp yet, or you expect to make decisions on the way;
- you want to see the code take shape and correct it as it goes.

**Use `autopilot` when**

- the plan is solid: observable acceptance criteria, no open decision on the path;
- the slice follows a pattern earlier slices already set;
- you want pull requests to review rather than a conversation to follow;
- several slices are independent and you want them in parallel, or you want to step away while it runs;
- everything it needs can be done without you: no secrets, no device, no manual step.

**Avoid `autopilot` for** the walking skeleton (use `bootstrap` and `deliver`), anything involving signing, credentials or a physical device, and slices that need a decision you have not thought about.

**Things to know about `autopilot`**

- Each agent works from the brief and the project files only, so the better `PLAN.md`, `DESIGN.md` and `TESTING.md` are, the better the result. A weak plan produces blocked slices and decision requests.
- It costs more than `deliver`: expect several agent runs per slice, more with QA rounds.
- Parallel slices edit the same tracking files (`PLAN.md`, `STATUS.md`, `CHANGELOG.md`); expect small merge conflicts there, resolved by keeping both sides.
- In a non-interactive session the tools it needs (git, your test command, your forge CLI) must be pre-authorised; a refused command is reported rather than skipped. In an interactive session you are simply asked.
- After merging, clean up with the commands it gives (`git worktree remove <path>`, `git branch -d <branch>`).

**A good rhythm**: `deliver` for the first slices until patterns and tests are established, then `autopilot` for well-specified slices, and your own review of each pull request before merging.

## 17. Verify independently (`verifier`)

An agent that runs the checks and judges each acceptance criterion as **verified, failed or unverifiable, with evidence**. It also checks that the tests would really catch a regression (no empty assertions, nothing skipped or weakened), conformance to the architecture and design, scope, and that the documents were updated. Verdict: Accept, Accept with notes, or Reject. It reports and never fixes. Ask for it directly ("use the verifier agent on SL2") or accept `deliver`'s offer.

### And review the diff (`pr-reviewer`)

An agent that reviews a branch or pull request like a human reviewer would: it reads the real diff and reports bugs and unhandled cases, readability and duplication, deviations from the codebase's conventions and the ADRs, risk (security, dependencies, breaking changes, migrations), scope creep and a description that does not match the diff. Findings are ranked **Blocking / Important / Suggestions** with `file:line`, and it ends with a verdict (`approve`, `comments`, `changes-requested`) and a ready-to-post comment. It does not re-run the acceptance criteria (the `verifier` does that), and it never edits, posts or approves anything. Ask for it directly ("use the pr-reviewer agent on this branch against main") or let `autopilot --review` run it.

## 18. Release (`publish`)

```
/trellis:publish
```

Pre-release checks (clean tree, planned slices done, full tests, release gates, changelog), a proposed version, release notes, a build from the release commit (including **Apple signing and notarisation**, app stores, packages or containers as your ADRs say), a **check of the built artifact in a clean environment**, then the release itself and a rollback plan. Every outward step (tag, upload, store submission, deploy, announcement) needs your explicit go.

## 19. Pick a project back up (`resume`)

```
/trellis:resume
```

Reads `STATUS.md`, `PLAN.md` and git, and answers in one screen: the phase, what is done, what is in progress (branch, uncommitted work), what is open or stale, and **one next action** with the exact command. It points out when the status document and the repository disagree. Read-only; it offers to refresh `STATUS.md` and does so only if you agree.

## 20. Change course (`change-course`)

```
/trellis:change-course drop the sync feature
```

For a new, modified or dropped requirement, a pivot or a technology change. It shows the **impact** across spec, ADRs, architecture, design, tests, plan and code, sizes the change and the cost of not changing, checks whether a kill criterion is touched, and proceeds on your decision. It then updates the documents **in dependency order** without renumbering anything: dropped requirements are marked `dropped`, changed decisions become new superseding ADRs.

## 21. Look back (`retro`)

```
/trellis:retro
```

For the end of a phase, a milestone, a release or the project. It gathers facts (slices planned versus done, spikes, decisions, changes), checks the result against the **original success and kill criteria**, asks for your own view, and ends with an explicit verdict: continue, pivot, park, abandon or done. Actions say where they will live. Written to `docs/retro/YYYY-MM-DD-title.md`.

## 22. See all your projects (`project-overview`)

```
/trellis:project-overview ~/projects
```

Reads each project folder (status, spec status, plan progress, git activity) and gives one comparable row per project with flags (stale, blocked, no status document, kill criterion near), then suggests what to **focus on, unblock and park**. Read-only; it writes a summary file only if you ask.

## 23. Automatic checks (`coherence-checker`, `security-reviewer`)

Two more read-only agents.

- **`coherence-checker`** checks that documents and code still agree: requirement IDs and coverage across the documents, ADR statuses and index, `STATUS.md` freshness, components, stack and interface code against the architecture and design, tests against the verification matrix. It says which side is probably right. Run it every few slices, after a change of course, and before a release.
- **`security-reviewer`** builds a lightweight **threat model** (assets, actors, trust boundaries, entry points) and checks personal data, secrets, authentication, input handling, storage and transport, dependencies and the release chain, with ranked findings and mitigations. It is a first pass, not a professional audit or legal advice.

## 24. Session status hook

When you open a session in a project that has a `STATUS.md`, Trellis adds its first lines to the context so Claude knows where the project stands (the file's content is treated as data, not instructions). It stays silent when there is no `STATUS.md`. Set the environment variable `TRELLIS_NO_STATUS=1` to turn it off.

## 25. Suggested order

```
incubate ─► (naming) ─► (prior-art) ─► (devils-advocate) ─► ux ─► architect ─► (spike) ─► ui
   ─► (design-reviewer, security-reviewer) ─► test-strategy ─► bootstrap ─► slice
   ─► deliver or autopilot ⟲ (verifier) ─► publish
```

For an **existing project**: `adopt` ─► (`security-reviewer`) ─► (`test-strategy` for the gaps) ─► `slice` (remaining work) ─► `deliver` or `autopilot`.

At any time: `resume`, `change-course`, `retro`, `project-overview`, `coherence-checker`, `adr`. Steps in parentheses are optional. Skip what does not apply (no interface: skip `ux`, `ui` and `design-reviewer`).

## 26. Adopt an existing project (`adopt`)

```
/trellis:adopt standard
/trellis:adopt full --draft
```

For a project that **already has code**. The code is the evidence and the documents are **hypotheses** until you confirm them.

1. **Scope.** You choose a level: `minimal` (the project's `CLAUDE.md` and `STATUS.md`), `standard` (plus `ARCHITECTURE.md`, retroactive ADRs, a `TESTING.md` baseline and an adoption report) or `full` (plus a `SPEC.md` of what exists and what is next, and a `PLAN.md` of the **remaining work only**).
2. **Inventory, read-only.** Stack, structure, verified build and test commands, tests and their gaps, CI and release, dependencies, git history (activity, most-changed files, conventions), existing documentation. It reports the existence of secrets-like files, never their contents.
3. **Short interview** on what the code cannot tell: purpose and users, state for the user, what is done and what is next, pain points, why the key choices were made, constraints, success and stop criteria. "I don't know" becomes an open question. With `--draft` there is no interview: every intent item becomes an open question, which suits non-interactive sessions.
4. **Drafts in `.trellis/adopt/`**, shown one by one. Each carries the banner *"Reconstructed on <date> from the code and history. To be confirmed."* and, per item, a **confidence** (high, medium, low) with its source. Retroactive ADRs have the status `accepted (reconstructed)`, list alternatives only when known, and say **"rationale unknown, to confirm"** rather than inventing one.
5. **Adoption report** (`ADOPTION.md`): what was verified versus inferred, open questions, findings (debt, risks, missing tests, hotspots) and next steps.
6. **Cross-check** of its own drafts, with an offer to run `coherence-checker` and `devils-advocate`.
7. **Promotion**: once you accept, the drafts are placed in their final locations. Existing documents are never overwritten: differences are shown and merged only as you accept. `--promote` does only this step later.

It never modifies code, tests or configuration, and it does not commit unless you ask. Afterwards, the useful steps are usually `security-reviewer`, the test gaps from the baseline, then `slice` for the remaining work and `deliver` or `autopilot`.

**To confirm an item**, edit the document and remove its "to be confirmed" mark; the confirmation log in `ADOPTION.md` keeps the trace.

## 27. Languages

Names (skills, agents, files) are in English. Documents are generated in the language of your project: the language you write in, unless the project already has documents in another one. Templates exist in English and French (`skills/<name>/templates/*.en.md` and `*.fr.md`).

## 28. Troubleshooting

| Symptom | Likely cause and fix |
| --- | --- |
| `/trellis:incubate` is unknown | The plugin is not loaded in this session. Check `claude plugin list`, start a **new session** after installing, or start Claude Code with `--plugin-dir`. |
| Claude does not use the skill when you describe an idea | Call it explicitly with `/trellis:incubate`. |
| It asks things you already wrote down | Put the notes in the project folder (README, notes) before starting, so it reads them in step 1. |
| The interview feels too long | Say so. The skill sizes the interview to the idea and can skip topics you have already settled. |
| `Invalid marketplace source format` | You passed an `ssh://…` URL. Use `git@host:owner/repo.git` instead (see section 1). |
| `Cannot add marketplace "trellis": its source doesn't match its extraKnownMarketplaces entry` | The marketplace name `trellis` is already declared from another source (a previous install from a mirror or a fork). Claude Code will not add the same name from a different source. Remove it, then add it again from the source you want: `claude plugin marketplace remove trellis`, then `claude plugin install trellis --marketplace <new source>`. Removing it also uninstalls the plugin, which the second command reinstalls. |
| `unable to get password from user` | You used an HTTPS URL on a private repository (a private fork, for instance). Use the SSH form. |
| The project status appears at the start of every session and I do not want it | Set the environment variable `TRELLIS_NO_STATUS=1`. |
| An update did not arrive | Run `claude plugin marketplace update trellis` then `claude plugin update trellis@trellis` in a terminal, and start a new session. |

## 29. Contributing and working on the plugin

Conventions for this repository (layout, naming, bilingual docs, "strict but soft") are in [`CLAUDE.md`](../CLAUDE.md). To check a change, run:

```sh
claude plugin validate .
```
