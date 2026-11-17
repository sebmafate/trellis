<img src="assets/icon.svg" width="96" height="96" alt="Trellis icon">

# Trellis

*Français : [README.fr.md](README.fr.md)*

A [Claude Code](https://claude.com/claude-code) plugin of skills and agents that carries a project from a raw idea to shipped software. It gives a project structure to grow on, without deciding its direction.

> Status: early work in progress (0.11.0). Licensed under the [MIT License](LICENSE).

## The idea

Each phase writes a file in the project's repository, and the next phase reads it. The files are the shared memory; nothing lives only in a chat.

```
idea ──► INCUBATION ──► DESIGN ─────────────────► DEVELOPMENT ────────► LIFE OF THE PROJECT
         SPEC.md        ARCHITECTURE.md + ADRs    PLAN.md               STATUS.md
                        DESIGN.md (UX, UI)        (deliverable slices)  CHANGELOG.md
                        TESTING.md
```

Trellis is **strict but soft**: skills point out what is missing (no validated spec, no acceptance criteria) and say what it risks, then let you decide. Overrides are recorded in the documents.

## Contents

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
| `follow-up` | skill | development (optional) | **available** |
| `publish` | skill | release | **available** |
| `resume` | skill | any | **available** |
| `change-course` | skill | any | **available** |
| `retro` | skill | any | **available** |
| `project-overview` | skill | any | **available** |
| `coherence-checker` | agent | any | **available** |
| `SessionStart` hook | hook | any | **available** |

Everything beyond the core path is **optional**: `naming`, `prior-art`, `autopilot`, its `--review` step and `follow-up` only run when you ask for them.

**See it before using it:** [examples/](examples/README.md) shows the documents of a small fictional project phase by phase, and the usage guide has an [example session](docs/usage.md) and a table of recipes ("what to type when...").

Ideas for later: a maintenance phase (dependency updates, deprecation), a skill for end-user documentation, a clickable-prototype skill, lessons carried from one project's retrospective to the next.

## Use it

```
claude plugin install trellis --marketplace https://github.com/sebmafate/trellis.git
```

The shorthand `--marketplace sebmafate/trellis` is accepted too. Run it in a terminal, then start a new session. (In Claude Code's interactive interface you can type `/plugin install …` instead; hosts such as T3 Code do not offer that command.)

Then ask Claude to incubate an idea, or invoke the skill directly as `/trellis:incubate`. Full guide (install options, trying it without installing, what each step does, troubleshooting): [docs/usage.md](docs/usage.md).

## What Trellis runs

Trellis is Markdown (skills and agent prompts) plus one small shell script. It has no server, no account, no telemetry and no dependency to install, and it does not read, store or send credentials. Here is everything it can do on your machine or on the network, and when.

**At the start of a session (hook, automatic).** The `SessionStart` hook (`hooks/session-status.sh`) reads the first 30 lines of `STATUS.md` in your project, if the file exists, and adds them to Claude's context, labelled as project data. It makes no network call and prints nothing when there is no `STATUS.md`. Set `TRELLIS_NO_STATUS=1` to turn it off.

**On your files, when you invoke a skill or agent.**
- Skills write the project's documents (`SPEC.md`, `ARCHITECTURE.md`, `PLAN.md`, `STATUS.md`, `docs/`…) and, for `autopilot`, `follow-up` and `adopt`, working files under `.trellis/`.
- The `developer` agent creates a branch and a git worktree, runs your project's own tests, lint and build commands, and makes local commits. It never pushes or merges.
- The read-only agents (`verifier`, `pr-reviewer`, `coherence-checker`, `product-owner`, `security-reviewer`) run read-only commands such as `git log`, `git diff` and your test commands, and edit nothing.

**On the web, only for research.** `naming`, `prior-art` and the `devils-advocate`, `design-reviewer` and `security-reviewer` agents can search and read public web pages. The queries are about your idea, the names you are comparing or the standards being checked, so they can contain what your project documents say about them.

**On your git forge, only when you ask.**
- `autopilot` pushes a branch (never with `--force`) and opens one pull request per slice **only if you authorised it**, using your existing `gh` (GitHub), `fj` (Forgejo or Gitea) or `glab` (GitLab, experimental) login. With `--review-comment` it posts one review comment per pull request.
- `follow-up` reads a pull request's review comments and checks, and pushes fixes to its branch and posts a summary comment only after the steps you agree to.
- `publish` asks for an explicit go before each outward step: committing, pushing a tag, uploading an artifact, creating a release, deploying.

Nothing else leaves your machine. If you only use the planning and design skills, Trellis stays within your project's files and the web research above.

## Languages

Names (skills, agents, files) are in English. Documentation and templates are in English and French; generated documents follow the language of the project.
