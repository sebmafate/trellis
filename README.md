<img src="assets/icon.svg" width="96" height="96" alt="Trellis icon">

# Trellis

*Français : [README.fr.md](README.fr.md)*

A [Claude Code](https://claude.com/claude-code) plugin of skills and agents that carries a project from a raw idea to shipped software. It gives a project structure to grow on, without deciding its direction.

> Status: early work in progress (0.3.0). Licensed under the [MIT License](LICENSE).

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
| `architect` | skill | design | **available** |
| `adr` | skill | design | **available** |
| `ux` | skill | design | **available** |
| `ui` | skill | design | **available** |
| `test-strategy` | skill | design | **available** |
| `slice` | skill | development | planned |
| `deliver` | skill | development | planned |
| `resume` | skill | any | planned |
| `devils-advocate` | agent | incubation, design | **available** |
| `design-reviewer` | agent | design | **available** |
| `verifier` | agent | development | planned |

Later candidates: `change-course`, `project-overview`, `publish`, `retro`, `prior-art`, `spike`, a coherence-check agent, a `SessionStart` hook.

## Use it

```
/plugin install trellis --marketplace https://git.vbmaf.net/sebastien/trellis.git
```

While the repository is private, use the SSH form instead: `--marketplace git@git.vbmaf.net:sebastien/trellis.git`.

Then ask Claude to incubate an idea, or invoke the skill directly as `/trellis:incubate`. Full guide (SSH install, trying it without installing, what each step does, troubleshooting): [docs/usage.md](docs/usage.md).

## Languages

Names (skills, agents, files) are in English. Documentation and templates are in English and French; generated documents follow the language of the project.
