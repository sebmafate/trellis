---
name: prior-art
description: Survey what already exists for an idea or a decision: products, open-source projects, platform-native features and commercial services, compared against the spec's requirements, with an honest build, buy, adapt or do-nothing verdict and dated sources. Use when the user asks "does this already exist", "what are the alternatives", "should I build or use something", or when an incubation needs more than a quick look. Writes docs/prior-art.md. Goes deeper than the quick check in `incubate`.
---

# Prior art

Find out what already solves the problem, how well, and what that implies: **build, adopt, adapt, or do nothing**. Output: `docs/prior-art.md`.

## Principles

- **Compare against the spec, not against features lists.** The yardstick is the requirements (`R1`...), the users and the constraints, so "better" means something.
- **Sources, with dates.** Every claim about a tool cites where it comes from and when it was checked. Markets move; an undated comparison rots.
- **Do not take marketing at face value.** Prefer documentation, repositories (activity, issues, last release), changelogs and independent reviews. Label what is a vendor claim.
- **Include the unglamorous options.** The platform's built-in feature, a spreadsheet, a script, a combination of two tools, and doing nothing.
- **Verdicts can kill the idea.** "Adopt X instead" is a valid and valuable result.
- **Keep what is useful from rivals**, too: conventions to follow, mistakes to avoid, gaps they leave.
- **Soft gates.** Flag missing material (no spec, vague requirements) and what that limits, then continue.

## Steps

### 0. Read the context

Read `SPEC.md` (problem, users, requirements, constraints, the riskiest assumption), any existing `docs/prior-art.md` (then this is an *update*: re-check dates and what changed), and the memory or notes you were given about the user's platform, skills and preferences. Template in `templates/` (`.en` / `.fr`), relative to this skill's directory.

### 1. Define the comparison

Agree with the user on the **criteria**: the Must requirements that discriminate, plus constraints that matter (platform, price, privacy and where data lives, licence, offline use, lock-in, maintenance, integration with tools the user already uses). Keep it to five to eight.

### 2. Search broadly

Look for: dedicated products, open-source projects, features of platforms or tools the user already has, commercial services, adjacent tools that could be bent to fit, and published approaches or papers when the problem is technical. Search in the languages and communities relevant to the project. If a deep-research capability or subagents are available, use them in parallel for the different families of candidates; otherwise search directly.

### 3. Evaluate the candidates

For each serious candidate, record: what it is, link, what it covers of the criteria (per requirement where possible), price or licence, maintenance signals (last release, activity, bus factor), lock-in and data portability, and **why it would or would not do for this project**. Mark which facts are verified in primary sources and which are claims.

### 4. Build, adopt, adapt or do nothing

Compare the options honestly, including the cost of building and maintaining. Give a recommendation with the reasoning and the conditions under which it would change. If the answer is "adopt X", say what is lost and what remains of the project, if anything.

### 5. Write and hand off

Write `docs/prior-art.md` from the template. Feed the result back: the spec's prior-art section and riskiest assumption, the kill criteria if an existing tool already meets the need, and `STATUS.md`. Suggest the next step: `incubate` to revise the spec, a decision to adopt, or `architect` to continue. If the `devils-advocate` agent is available, offer a review of the verdict.

## What not to do

- Do not compare on generic feature checklists unrelated to the requirements.
- Do not present undated or unsourced claims as facts.
- Do not stop at the first good-looking tool, or at the famous ones.
- Do not favor "build" because it is more fun; count the maintenance.
