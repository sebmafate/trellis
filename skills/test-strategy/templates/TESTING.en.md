# <Project name>: test strategy

| | |
| --- | --- |
| Status | draft / accepted |
| Version | 0.1 |
| Date | YYYY-MM-DD |
| Based on | `SPEC.md`, `ARCHITECTURE.md`, `DESIGN.md` |

## Goals and risks

Goal in a sentence or two.

| # | What would hurt most if wrong | Why it is risky | Where it is tested |
| --- | --- | --- | --- |
| T1 | | | |

Hard to test: ... (and the approach taken).

## Test levels

| Level | Covers | Tool | Runs where | When |
| --- | --- | --- | --- | --- |
| Unit | | | local, CI | every commit |
| Integration | | | | |
| End to end | | | | |
| Accessibility | | | | |
| Manual and exploratory | | | | |

Levels deliberately left out, and why: ...

## Verification matrix

| Requirement | Priority | Level | Concrete check (observable) | Automated |
| --- | --- | --- | --- | --- |
| R1 | Must | | | yes / no |

Success criteria measured only after release or in daily use:

| Criterion (from SPEC.md) | How it is observed | When |
| --- | --- | --- |

## Test data and environments

- Fixtures and factories: ...
- Synthetic versus real data, and where each lives: ...
- Doubles for external services: ...
- Control of time, randomness and concurrency: ...
- Environments: ...
- Migrations, sync conflicts and recovery: ...

## Quality gates

| Moment | What runs | Blocks | Warns |
| --- | --- | --- | --- |
| Each commit | | | |
| Each pull request | | | |
| Each release | | | |

Flaky test policy: ...

## Non-functional and manual testing

- Performance budgets: ...
- Security and privacy checks: ...
- Accessibility checks: ...
- Manual and exploratory checklist: ...
- Beta or dogfooding exit criteria: ...

## Definition of done (per slice)

- [ ] Tests written first or alongside, and passing
- [ ] Acceptance criteria verified
- [ ] Relevant rows of the verification matrix updated
- [ ] Documentation and `STATUS.md` updated
- [ ] No new warnings

## Tooling decisions

| Decision | ADR or note |
| --- | --- |
| | |

## Testability findings for the architecture

Anything that is hard to test because of how the system is built, to feed back to `architect`.

- ...

## Open points

- ...
