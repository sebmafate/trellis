# commit-report: Specification

| | |
| --- | --- |
| Status | validated |
| Version | 1.0 |
| Date | 2026-03-02 |

> Illustrative example. This project is fictional and the document was written by hand to show the shape of what `incubate` produces. It is not a transcript of a real run.

## Summary

A command-line tool that reads the git history of one or more local repositories and writes the monthly activity report a freelancer owes a client, as Markdown.

## Problem

Each month the user rebuilds an activity report by scrolling through `git log` in several repositories and retyping what was done. It takes about an hour, it is boring, and details get forgotten. The user is the only user.

## Goals and success criteria

- G1. A month's report is ready to send in **under 10 minutes**, including a read-through.
- G2. The tool is used **three months in a row** (the real test that it is worth keeping).

## Scope

**In (first usable version)**

- Read local git repositories, for one calendar month.
- Group the work by day and by project, from commit subjects.
- Skip merge commits and other people's commits by default.
- Write Markdown (and CSV if cheap).

**Out (non-goals)**

- No hosting, no web interface, no account.
- No time tracking and no invoicing integration.
- No call to a remote service, forge or AI: the history never leaves the machine.

## Requirements

| ID | Requirement (what, not how) | Priority | How to verify |
| --- | --- | --- | --- |
| R1 | Produce a report for a given month from one or more repositories | Must | Golden-file test on a fixture repository |
| R2 | Group entries by day and by project, listing commit subjects | Must | Golden-file test |
| R3 | Exclude merge commits and commits by other authors by default; an option includes all authors | Must | Test with a fixture holding merges and two authors |
| R4 | Also write CSV | Should | Test on the CSV header and one row |
| R5 | Read a config file that maps repositories to client names | Should | Test with a sample config |
| R6 | Make no network connection | Must | Test that runs the command with sockets disabled |
| R7 | Estimate hours from gaps between commits | Could | Not planned for the first version |

## Constraints and preferences

Runs on the user's laptop (macOS and Linux). Must work offline. Prefers a language already known to the user and a tool that is easy to install. Technology is decided in `ARCHITECTURE.md`.

## Prior art

Surveyed in [docs/prior-art.md](docs/prior-art.md): a `git log` one-liner already gives about half of what is needed. Verdict: run that as the cheapest test, then build small.

## Risks and assumptions

| # | Assumption or risk | Impact if false | Status |
| --- | --- | --- | --- |
| A1 | **Commit subjects are good enough to read as a report** (the riskiest) | The report needs heavy rewriting and saves no time | untested |
| A2 | One month fits comfortably on a page or two | Output too long to review in 10 minutes | untested |
| R-1 | Commit messages are in several languages | Mixed-language report | accepted |

## Cheapest test

Format last month's history of two repositories with a one-line `git log` command and a small shell loop. Read it as if sending it to a client. Time the editing. About one hour, no application written.

## Kill criteria

- If editing the raw output of the test takes more than **20 minutes**, stop: the commit subjects are not a usable base (A1 false).
- If after two months of use the report still takes more than 20 minutes, stop.

## Open questions

- Should several repositories be merged into one list per day, or kept in sections per client? (decided in `DESIGN.md`)

## Decision log

| Date | Decision or override | Reason |
| --- | --- | --- |
| 2026-03-02 | Verdict: test first, then proceed | A1 is untested |
| 2026-03-04 | A1 confirmed by the test (12 minutes of editing): proceed | Below the 20-minute kill criterion |
