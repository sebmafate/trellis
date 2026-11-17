---
name: spike
description: 'Frame, run and record a spike: a small, time-boxed experiment that answers one question about feasibility, performance, an API or a risky assumption before committing to a decision, with success criteria set before running and an honest written verdict. Use when the architecture, an ADR or the spec has an unknown that could change the design ("we don''t know if X works", "is Y fast enough", "can we do Z on this platform"), or when the user wants to de-risk something cheaply. Results feed back into ADRs, the architecture and the risk list.'
---

# Spike

A spike buys **information, not product**. It answers one question, within a time box, against criteria written down *before* the experiment, and ends with a verdict that changes (or confirms) a decision. The code is throwaway.

## Principles

- **One question per spike.** If there are two questions, there are two spikes.
- **Criteria before running.** Write what result means "yes", what means "no", and what is inconclusive, before you start. Otherwise the result gets bent to fit.
- **Time box and stop.** Fix a limit (hours to a couple of days). At the limit, record what you have, including "inconclusive". Do not extend silently.
- **Throwaway code.** Spike code lives in `spikes/` or on a spike branch and is never promoted to production as is. If it proves the approach, `deliver` rebuilds it properly under tests.
- **A negative result is a result.** "It does not work, and here is why" is exactly what a spike is for.
- **Feed the decision.** A spike that changes nothing was either unnecessary or not recorded properly.
- **Use real conditions where they matter.** A performance question needs realistic data and the real device or platform; say when it was not possible.
- **Soft gates.** Flag a missing prerequisite and its risk, then let the user decide.

## Steps

### 0. Read the context

Read `SPEC.md` (riskiest assumption, kill criteria), `ARCHITECTURE.md` (risks and spikes table), the ADRs with status `proposed` or open points, and `docs/spikes/` for existing spikes (to avoid repeating one, and to pick the next number). Template in `templates/` (`.en` / `.fr`), relative to this skill's directory.

### 1. Frame the question

State it as a yes/no or a measurable question ("Can a share extension write to the shared store in under 200 ms while the app is open?"). Say **why it matters**: which requirement, ADR or risk it serves and what the project does differently depending on the answer. If no decision depends on the answer, advise against the spike.

### 2. Criteria and time box

Agree with the user: the success criteria (observable, with numbers when relevant), the failure criteria, what counts as inconclusive, the time box, and the conditions (data, devices, versions). Record them in the spike file **now**.

### 3. Design the smallest experiment

The cheapest thing that can answer the question: a script, a tiny app, a benchmark, reading the source or documentation, asking a vendor. Prefer an experiment that can fail fast. Agree where the throwaway code lives.

### 4. Run it

Run the experiment, or guide the user through the parts that need their device or accounts. Keep notes of what was done and what was observed, including surprises. Stop at the time box.

### 5. Record the result and verdict

Fill in the result with the observed data (numbers, outputs, links to the throwaway code), compare with the criteria, and give a verdict: **confirmed**, **refuted** or **inconclusive**, with what remains unknown.

### 6. Feed back

- The linked ADR: `proposed` becomes `accepted`, or is superseded, through the `adr` skill. Do not edit an accepted ADR.
- `ARCHITECTURE.md`: update the risks and spikes table and anything the result changes.
- `SPEC.md`: if the riskiest assumption or a kill criterion is touched, say so; if a kill criterion is met, recommend `retro`.
- `PLAN.md`: add, remove or reorder slices if the result changes the work.
- `STATUS.md`: next action.

## What not to do

- Do not start a spike without a question and criteria.
- Do not let a spike become the product.
- Do not report a spike as confirmed on weak evidence; say inconclusive.
- Do not run a spike when the answer would not change anything.
