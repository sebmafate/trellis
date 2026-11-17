# 0001. Python, standard library only

- Status: accepted
- Date: 2026-03-05
- Serves: R6, constraints (known language, easy install)

> Illustrative example.

## Context

The tool is small, runs on the user's laptop, and must make no network connection (R6). The user knows Python and wants as few things to maintain as possible.

## Options considered

| Option | For | Against |
| --- | --- | --- |
| Python, standard library only | Known by the user; no dependency to update or audit; trivial to keep offline | Needs Python installed; no single binary |
| Go | Single static binary | Unfamiliar to the user; slower to write for a tool this small |
| Shell script | Nothing to install | Hard to test and to keep readable once grouping and config appear |

## Decision

Python 3.12 and the standard library only (`argparse`, `subprocess`, `tomllib`, `csv`, `datetime`).

## Consequences

- No dependency to track, which also makes R6 easy to enforce.
- Distribution is `pipx install .` or running the module; accepted for a one-person tool.
- Revisit if the tool is ever shared with people who have no Python.
