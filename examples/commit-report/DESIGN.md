# commit-report: design (command line)

> Illustrative example. For a command-line tool, the commands, their output and their errors are the interface.

## Journeys

| ID | Journey | Serves |
| --- | --- | --- |
| J1 | Produce last month's report for the repositories in the config | R1, R2, R5 |
| J2 | Produce a report for one repository, without a config | R1, R2 |
| J3 | Include other authors | R3 |

## Commands

```
commit-report month 2026-02 [--repo PATH ...] [--config FILE] [--format md|csv] [--all-authors]
```

Without `--repo`, the repositories come from `commit-report.toml` in the current folder.

## Output (screen S1: the report)

```
# Activity report: February 2026

## Client A: project-one

### 2026-02-03
- Add retry to the export job
- Fix date parsing in the importer

### 2026-02-05
- Document the configuration file
```

## States and messages

| State | Message (stderr) | Exit code |
| --- | --- | --- |
| No commit in the month | `No commits found for 2026-02 in 2 repositories.` | 0 |
| Path is not a git repository | `Not a git repository: ./notes. Check the path or remove it from the config.` | 2 |
| Bad month | `Month must look like 2026-02, got "feb".` | 2 |
| Config file invalid | `commit-report.toml line 4: expected a string for "client".` | 2 |

## Vocabulary

"report", "month", "client", "project", "entry": always these words, never "log" or "summary".

## Open question closed

Sections per client, days inside each (answers the open question of the spec).
