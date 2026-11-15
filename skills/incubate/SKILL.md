---
name: incubate
description: Turn a raw idea into a validated project spec (SPEC.md) through a one-question-at-a-time interview that challenges the idea instead of just recording it. Use when the user has an idea, says "I want to build X", asks whether an idea deserves to become a project, or wants a requirements document / PRD / cahier des charges. Covers problem, users, scope and non-goals, requirements, risks, cheapest test and kill criteria. Does not choose technology or architecture (that is the `architect` skill).
---

# Incubate

Take an idea and produce `SPEC.md`: a short, honest document that says what is being built, for whom, what is **not** being built, and when to stop. You are an interviewer who challenges, not a scribe who transcribes.

## Principles

- **One question at a time.** Never a questionnaire. Propose a recommended answer so the user can reply "ok" or correct it.
- **Do not invent.** If an answer is a guess, write it as an assumption. Never fill a section just to fill it.
- **Challenge the idea.** "Does this deserve to exist?" is a legitimate outcome. A project that is parked or abandoned after incubation is a success, not a failure.
- **No technology choices here.** Note platform or language preferences as *constraints* or *preferences* and defer the decision to `architect`.
- **Soft gates.** Flag gaps and say what they risk, then let the user decide. Record any override in the decision log. Never block.
- **Stay small.** A small tool deserves 5 questions and a one-page spec; a product deserves more. Size the interview to the idea.

## Steps

### 0. Read the context first

Before asking anything, look around the working directory and the memory you were given: an existing `SPEC.md` (then this is an *update*, not a restart), `README`, notes, idea lists, earlier discussions. Do not re-ask what is already answered; confirm it instead ("I read that X, still true?").

Pick the document language: the language the user writes in, unless the project already has documents in another one. Use the matching templates in `templates/` (`.en` / `.fr`), relative to this skill's directory.

### 1. Capture the seed

Restate the idea in two sentences and ask the user to confirm or correct. Ask for a working name only if there is none.

### 2. Interview

Walk through these topics in order, skipping any that context already answers. For each, ask one question, offer your best guess, and follow up only when the answer is vague.

1. **Problem and user**: who has the problem, how do they cope today, how painful is it? (If the user is themself, say so; it is a legitimate answer.)
2. **Why this, why now**: what makes it worth the effort compared to the alternatives, including doing nothing?
3. **Success**: how will you know it worked? Push for something observable ("I use it weekly", "a CRA takes 10 min").
4. **Scope**: what is in the first usable version? What is explicitly **out**? Non-goals are mandatory; if the user has none, propose three.
5. **Constraints**: platform, budget, time available, privacy, legal, who will maintain it, existing tools to reuse or integrate with.
6. **Prior art**: what already exists? Offer a quick web search; for a deep, sourced survey with a build-or-adopt verdict, suggest the `prior-art` skill.
7. **Riskiest assumption**: the one thing that, if false, kills the idea. Name it with the user.
8. **Cheapest test**: the smallest experiment (hours or days, ideally without writing the app) that checks that assumption.
9. **Kill criteria**: what result of the test, or what cost overrun, means "stop"? A spec without a kill criterion is incomplete.

### 3. Challenge

Before writing, say out loud what worries you: scope creep, a vague success criterion, an untested assumption, an existing tool that already does 80%. Keep it to the two or three points that matter. If the `devils-advocate` agent is available, offer an independent review once the draft exists.

### 4. Write `SPEC.md`

Fill the template. Rules for the content:

- Each requirement gets a stable ID (`R1`, `R2`, ...), a priority (**Must / Should / Could**), and *how it will be verified*. These IDs are what `architect`, `slice` and `deliver` trace against later, so do not renumber them afterwards.
- Requirements say *what*, never *how*. "Works offline" is a requirement; "uses SQLite" is not.
- Assumptions, open questions and unresolved points go in their own sections, not hidden in prose.
- Aim for one to three pages. Cut rather than pad.

Also create a minimal `STATUS.md` (phase: incubation, next action, date) so `resume` works from day one. Do not overwrite an existing one; update its phase and next action instead.

### 5. Verdict

End with an explicit recommendation, one of:

| Verdict | Meaning |
| --- | --- |
| **Proceed** | Spec is solid enough; next step is `architect` |
| **Test first** | The riskiest assumption must be checked before investing; describe the test |
| **Park** | Not now; say what would reopen it |
| **Abandon** | Not worth building, with the reason |

List any gaps that remain (missing success metric, untested assumption, open questions). If the user wants to proceed anyway, do it, and log the override in the spec's decision log with the date and the gap accepted.

Set the spec's status accordingly: `draft`, `validated`, `parked` or `abandoned`.

### 6. Hand off

Tell the user where the files are and what comes next (`architect`, or the test, or nothing). Do not start the next phase unprompted.

## What not to do

- Do not write the architecture, pick a stack, or sketch a data model.
- Do not produce a feature wishlist. Every "Must" should survive the question "would the first version be useless without it?".
- Do not validate a spec just because the user is enthusiastic. Enthusiasm is not a success criterion.
