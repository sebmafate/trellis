# 0002. Read git through the `git` command

- Status: accepted
- Date: 2026-03-05
- Serves: R1, R3, R6

> Illustrative example.

## Context

The tool needs the commits of a month, with author, date and subject, including whether a commit is a merge.

## Options considered

| Option | For | Against |
| --- | --- | --- |
| Run `git log` with a fixed format through `subprocess` | Same behaviour as the user's own git; no dependency; merges and authors handled by flags | Requires `git` on the path; output must be parsed |
| A Python binding for git internals | No subprocess | A dependency (breaks ADR 0001) and a larger surface |

## Decision

Call `git log --no-merges --author=... --since=... --until=... --format=<fixed format>` and parse the output.

## Consequences

- Behaviour follows the installed git; tests use a fixture repository built with real git commands.
- Parsing is isolated in one module so the format can change in one place.
