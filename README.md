<img src="assets/icon.svg" width="96" height="96" alt="Trellis icon">

# Trellis

*Français : [README.fr.md](README.fr.md)*

A [Claude Code](https://claude.com/claude-code) plugin of skills and agents that carries a project from a raw idea to shipped software. It gives a project structure to grow on, without deciding its direction.

> Status: early work in progress (0.10.0). Licensed under the [MIT License](LICENSE).

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
| `publish` | skill | release | **available** |
| `resume` | skill | any | **available** |
| `change-course` | skill | any | **available** |
| `retro` | skill | any | **available** |
| `project-overview` | skill | any | **available** |
| `coherence-checker` | agent | any | **available** |
| `SessionStart` hook | hook | any | **available** |

Ideas for later: a maintenance phase (dependency updates, deprecation), a skill for end-user documentation, a clickable-prototype skill.

## Use it

```
claude plugin install trellis --marketplace https://github.com/sebmafate/trellis.git
```

The shorthand `--marketplace sebmafate/trellis` is accepted too. Run it in a terminal, then start a new session. (In Claude Code's interactive interface you can type `/plugin install …` instead; hosts such as T3 Code do not offer that command.)

Then ask Claude to incubate an idea, or invoke the skill directly as `/trellis:incubate`. Full guide (install options, trying it without installing, what each step does, troubleshooting): [docs/usage.md](docs/usage.md).

## Languages

Names (skills, agents, files) are in English. Documentation and templates are in English and French; generated documents follow the language of the project.
