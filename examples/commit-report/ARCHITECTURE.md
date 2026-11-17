# commit-report: architecture

| | |
| --- | --- |
| Status | accepted |
| Date | 2026-03-05 |

> Illustrative example.

## Drivers

Offline only (R6), tiny and easy to maintain, testable with a fixture repository, one user.

## Overview

```
 CLI (argparse) ──► config loader ──► collector (git log) ──► grouper ──► renderer (Markdown, CSV)
```

Data flows one way. Nothing is stored between runs.

## Components

| Component | Responsibility | Depends on |
| --- | --- | --- |
| `cli` | Parse arguments, call the others, map errors to exit codes | all |
| `config` | Read the optional `commit-report.toml` (repository to client name) | standard library |
| `collector` | Run `git log` for a repository and a month, return commit records | the `git` command |
| `grouper` | Group records by day and project | none |
| `renderer` | Turn groups into Markdown or CSV text | none |

## Decisions

| ADR | Decision |
| --- | --- |
| [0001](docs/adr/0001-python-standard-library.md) | Python, standard library only |
| [0002](docs/adr/0002-read-git-through-the-cli.md) | Read git through the `git` command |

## Traceability

| Requirement | Components | ADR |
| --- | --- | --- |
| R1 | cli, collector | 0001, 0002 |
| R2 | grouper, renderer | 0001 |
| R3 | collector | 0002 |
| R4 | renderer | 0001 |
| R5 | config | 0001 |
| R6 | all (no network code anywhere) | 0001 |

Coverage: every Must requirement is carried by at least one component and one decision.

## Risks

- Parsing git output breaks if the format changes: isolated in `collector`, covered by the fixture test.
