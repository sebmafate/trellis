---
name: adr
description: Record one architecture decision as a numbered Architecture Decision Record in docs/adr/, with the context, the options considered (including the rejected ones and why), the decision and its consequences. Use when a significant technical choice has been made or is being made ("we decided to use X", "let's record this", "why did we pick Y?"), when a past decision changes (supersede it), or when the `architect` skill needs a decision written down. Not for trivial choices that are cheap to reverse.
---

# ADR: record a decision

An ADR keeps the **why** of a decision after the discussion is forgotten. One decision per file, numbered, never rewritten. A reader six months from now (including you) should understand what was chosen, what was rejected, and what would make us reconsider.

## Principles

- **One decision per ADR.** If you find "and" in the title, split it.
- **Worth an ADR only if it is hard to reverse or surprising.** Language, platform, storage, sync strategy, hosting, major dependency, auth model: yes. A variable name, a lint rule: no. When in doubt, ask whether a newcomer would wonder "why on earth did they do it this way?".
- **Record the rejected options.** An ADR with a single option is an announcement, not a decision. Each rejected option needs a real reason, not a strawman.
- **Tie it to the spec.** Cite the requirement IDs (`R3`) and constraints that drive the decision. A decision that serves no requirement is a smell; say so.
- **Immutable once accepted.** Do not edit an accepted ADR's content. When the decision changes, write a new ADR that **supersedes** it. Only the old one's status line changes.
- **Soft gates.** If the context is thin, flag what is missing and write the ADR with the gaps listed under "Open points" rather than refusing.

## Steps

### 1. Locate and read

Find `docs/adr/` in the project (create it if absent). List existing ADRs to pick the next number (four digits, `0001`, `0002`, ...), and to detect whether the new decision duplicates or contradicts an earlier one. Read `SPEC.md` for requirement IDs and constraints, and `ARCHITECTURE.md` if present.

Language of the ADR: the project's, as for the other documents. Templates are in `templates/` (`.en` / `.fr`), relative to this skill's directory.

### 2. Gather only what is missing

From the conversation and the files, fill what you can. Ask one question at a time for the rest:

1. What is being decided, in one sentence?
2. What forces are at play (requirements, constraints, deadlines, skills, cost)?
3. Which options were seriously considered? (Propose any obvious one that is missing, including the boring or do-nothing option.)
4. What was chosen, and why this one over the others?
5. What does it cost us, and what could go wrong?
6. What would make us revisit it? (A measurable trigger beats "if things change".)

### 3. Write the file

`docs/adr/NNNN-short-kebab-title.md` from the template. Status: `proposed` while the user is still deciding, `accepted` once confirmed. Date in ISO format.

Keep it to one page. Consequences must include the **negative** ones.

### 4. Keep the index in sync

- Maintain `docs/adr/README.md`: a table of number, title, status, date. Create it if missing.
- If `ARCHITECTURE.md` exists, add or update the row in its decisions index and, where relevant, the traceability table.

### 5. Superseding or deprecating

- **Superseding**: write the new ADR with `Supersedes: NNNN`, then change only the status of the old one to `superseded by MMMM`. Update the index.
- **Deprecating** (decision no longer applies, nothing replaces it): set status `deprecated` with a one-line reason.
- Never delete an ADR and never renumber. History is the point.

### 6. Report

Say which file was written, its status, and whether anything remains open (a spike to run, a question for the user, an unconfirmed assumption).

## What not to do

- Do not record a decision nobody actually made. If the user is undecided, write a `proposed` ADR or none.
- Do not pad with generic pros and cons. Each point must be true of *this* project.
- Do not choose for the user. You may recommend, but the decision line states what the user decided.
