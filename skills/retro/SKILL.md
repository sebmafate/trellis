---
name: retro
description: Run a retrospective at the end of a phase, a milestone, a release or a project: compare what happened with the spec's success and kill criteria, review estimates, decisions and spikes against reality, capture what worked and what did not, and reach an explicit verdict (continue, pivot, park, abandon, done) with concrete actions. Use when the user asks for a retrospective, a post-mortem or a review of the project, when a kill criterion may be met, or after a release. Writes docs/retro/YYYY-MM-DD-title.md.
---

# Retro

Look back with **evidence**, decide what to do next, and capture lessons so they are used. The most important output is an explicit verdict about the project, not a list of feelings.

## Principles

- **Evidence first.** Use `STATUS.md`, `PLAN.md`, the git history, `CHANGELOG.md`, the ADRs, the spikes and the spec's criteria. Add the user's own impressions as a clearly separated input.
- **Test against the original bet.** The spec said what success is and when to stop. Check the result against those, not against a story rewritten afterwards.
- **A verdict, not a mood.** End with continue, pivot, park, abandon or done, and the reason.
- **Blameless.** Look at decisions, information and process; not at people. When the user is the only person involved, that still holds: what did we know then?
- **Actions with an owner and a place.** A lesson that does not change a document, a habit or a tool will be forgotten.
- **Learn about the process too**, including how these Trellis skills helped or got in the way.
- **Soft gates.** Flag missing material (no success criteria, no plan) and what that limits, then continue with what exists.

## Steps

### 0. Scope and gather

Agree on the scope (a phase, a milestone, a release, the whole project) and the period. Read the documents and the git history for that period. Compute simple facts: slices planned versus done, sizes assumed versus the real time if known, commits and activity, spikes run and their verdicts, ADRs added or superseded, requirements changed or dropped, defects found by the `verifier`.

### 1. Check the bet

Go through the spec:

- **Success criteria**: met, partly met, unknown. Evidence for each.
- **Riskiest assumption**: what is now known?
- **Kill criteria**: met? near? still the right ones?
- **Scope**: what was added, cut or deferred, and was it right?

### 2. Review the work

- **Estimates versus reality**: where were the surprises, and why?
- **Decisions**: any ADR whose "revisit when" trigger has been reached, any that proved wrong, any that proved their worth?
- **Quality**: defects, rework, test gaps, drift between documents and code.
- **Process**: where the documents helped, where they were overhead.

### 3. Ask the user

A few questions, one at a time: what went well, what was frustrating, what was dropped or avoided, what surprised you, is it still worth your time and attention? Record the answers as the user's impressions, separate from the evidence.

### 4. Verdict

State one: **Continue** (what is next), **Pivot** (what changes; use `change-course`), **Park** (what would reopen it), **Abandon** (why, and what to salvage), **Done** (success criteria met; maintenance only). Explain it in a few lines with the evidence. The user decides; recommend with reasons.

### 5. Actions

List concrete actions, each with a place where it will live: a change to the spec or plan, an ADR to write, a spike to run, a habit to adopt, a tool or skill to improve (including Trellis). Keep it to a handful.

### 6. Write and update

Write `docs/retro/YYYY-MM-DD-title.md` from the template (`templates/`, `.en` / `.fr`, relative to this skill's directory). Update `STATUS.md` and the spec's status to match the verdict, and add a line to the spec's decision log. Suggest the next step.

## What not to do

- Do not conclude "go on" because stopping feels bad. Check the kill criteria honestly.
- Do not produce a long list of lessons with no actions.
- Do not rewrite the original success criteria to fit the results.
- Do not skip the user's own view of whether the project is still worth it.
