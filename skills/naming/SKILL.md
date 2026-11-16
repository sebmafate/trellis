---
name: naming
description: Help find an original name for a project, then check each finalist for obvious conflicts (existing products in the same field, domain names, repository and package names, app stores, meaning in other languages) and record the choice. Optional: it always asks whether the user wants a name before doing anything. Use when the user says they need a name, is hesitating between names, wants a name checked, or when `incubate` finds the project has only a working name and the user wants help. Writes docs/naming.md. A first screening, not a trademark search or legal advice.
---

# Naming

Find a name that is **original, easy to say and spell, meaningful for the project, and not already taken in its field**, and say honestly what was checked and what was not. Output: `docs/naming.md`.

## Principles

- **Optional, asked first.** Ask "do you want help finding a name?". If the answer is no, stop and do nothing. A working name is a legitimate answer; renaming later is cheap early and expensive after a release, so say so once, without insisting.
- **A few good candidates beat many.** About a dozen to start, five finalists at most. A list of fifty is noise.
- **Originality means something.** Avoid the generic descriptive name every competitor could use, and avoid names that echo a well-known brand too closely. Say when a candidate looks too close to something you know.
- **Never claim a name is free.** Report what you searched, where and when, and what you could not check. "I found no conflict in these places" is the strongest honest statement.
- **Not legal advice.** A web screening does not replace a trademark search. If the project is commercial, say so and suggest a professional search in the relevant registers before investing in the name.
- **Keep the user's privacy.** Searches use the candidate names and a generic description of the field ("a note-taking app for musicians"), never the user's identity, employer or private project details.
- **The user decides.** You propose, rank and explain; you do not pick for them.
- **Soft gates.** Flag a missing input and what it limits, then continue.

## Steps

### 0. Read the context

Read `SPEC.md` (problem, users, scope, constraints, whether it is personal, open source or commercial), any existing `docs/naming.md` (then this is an *update*: re-check the dates), and the notes you were given. If the project already has a name and the user only wants it checked, go straight to step 4 with that single candidate. Pick the document language as `incubate` does and use the template in `templates/` (`.en` / `.fr`), relative to this skill's directory.

### 1. Confirm the need

Ask whether the user wants help finding a name, and whether names must work in one language or several. If there is no spec yet, a short description of the project is enough; suggest `incubate` first only if the idea itself is still vague.

### 2. The brief

One question at a time, with your best guess offered, only for what the context does not already answer:

1. **What should it evoke?** Ideas, images, a feeling; and what it must **not** suggest.
2. **Who will say and read it?** Languages, audience, spoken or typed (a CLI name is typed all day; an app name is said aloud).
3. **Constraints.** Length, characters to avoid, accents, a prefix or suffix wanted or hated, names the user already likes or dislikes and why.
4. **Where must it be available?** A domain (which endings), a repository name, a package name (npm, PyPI, crates, Homebrew...), an app store, a social handle. Not all matter for every project.
5. **Where will it live?** Personal, open source, internal or commercial: this sets how much checking is worth doing.

### 3. Propose

Offer about a dozen candidates spread over different directions, so the user sees real choices:

- **Metaphor**: an image from nature, craft, music, sport or another field that matches the idea.
- **Coined or blended word**: invented, short, ownable.
- **Evocative descriptive**: hints at the use without being generic.
- **Other languages**: a word with a fitting meaning, checked for how it reads and sounds elsewhere.

For each: the name, what it evokes, why it fits, and any known risk (hard to pronounce or spell, a meaning in another language, resemblance to something known). Then help the user cut down to **three to five finalists**, and ask for reactions in plain words ("too long", "too cute") to refine, rather than starting again.

### 4. Screen the finalists

For each finalist, search the web and record **where, what and when**:

- **Existing products and projects** with the same or a very similar name, first in the same or an adjacent field, then anywhere well known. Note how close they are (same word, same sound, same field).
- **Domain names** that matter to the project: whether the main ones appear taken or parked, and by what. Say that registration status can only be confirmed with a registrar.
- **Repositories and packages**: GitHub (user, organisation, repository), and the package registries relevant to the stack.
- **App stores**, when the project is an app.
- **Meaning and sound in other languages** the audience speaks; anything offensive, comic or confusing.
- **Public trademark registers**, only where a search can really be done from here; otherwise list the registers to check (national office, the EU office, the US office, the international database) and say they were **not** checked.

Verdict per finalist: **no conflict found in the places checked / close conflict (say with what) / blocking conflict**. Say what was not checked.

### 5. Decide and record

Present the finalists with their verdicts and your recommendation, with the reasoning, and let the user choose. Then:

- Write `docs/naming.md` from the template (brief, candidates considered and why rejected, finalists with screening results and dates, the decision).
- If `SPEC.md` exists, put the name in its title and add a line to the decision log. Offer to update `README` and `STATUS.md`; do not rename folders, repositories or files without being asked.
- If the user keeps a working name, record that, and note when to revisit (before the first public release).

### 6. Hand off

Say where the file is and what the next step is (usually back to `incubate` or `architect`). If the project is commercial, remind the user once that the screening is not a trademark clearance. Do not start the next phase unprompted.

## What not to do

- Do not start without the user's yes, and do not insist after a no.
- Do not say a name is "available", "free" or "safe". Say what you checked.
- Do not register anything, buy a domain, create a repository or contact anyone.
- Do not put the user's identity or private project details in search queries.
- Do not drown the user in options: a dozen at most, then a few finalists.
- Do not present a justification you made up after the fact: the reasons for a name are what the user said it should evoke.
