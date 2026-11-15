# Trellis: conventions for working on this repository

This repository is a Claude Code plugin and is meant to become public. Write accordingly.

## Layout

- `.claude-plugin/plugin.json`: plugin manifest
- `skills/<name>/SKILL.md`: one skill per directory; its templates live in `skills/<name>/templates/`
- `agents/<name>.md`: one agent per file
- `hooks/hooks.json` and `hooks/*.sh`: plugin hooks (currently the `SessionStart` status hook)
- `docs/`: design notes and decisions about Trellis itself

## Conventions

- **Names in English**: skills, agents, files, directories, code. Documentation and templates in English **and** French (`*.en.md`, `*.fr.md`, or `README.md` / `README.fr.md`).
- **Strict but soft**: a skill flags a missing prerequisite and its risk, then lets the user continue. It never refuses outright. Overrides are logged in the project's documents.
- **Documents are the memory**: each phase writes a file in the user's project; the next phase reads it. Nothing important should live only in the chat.
- **Stable IDs**: requirements are `R1`, `R2`...; decisions are `docs/adr/NNNN-title.md`. Later phases trace against them, so skills must never renumber.
- **Keep skills lean**: a `SKILL.md` states the method and the rules; long templates go in `templates/`. Descriptions say when to use the skill and when not to.
- **No personal data** in this repository (names, paths, project details): it will be public.
- Do not commit unless asked.
