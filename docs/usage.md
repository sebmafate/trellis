# Using Trellis

*Français : [usage.fr.md](usage.fr.md)*

Trellis is a [Claude Code](https://claude.com/claude-code) plugin. It adds skills (and, later, agents) that guide a project from a raw idea to shipped software. This page explains how to install it and how to work with it.

## 1. Install

You need Claude Code. Then install the plugin from this repository's marketplace:

```
/plugin install trellis --marketplace https://git.vbmaf.net/sebastien/trellis.git
```

If the repository is private or your Git is set up for SSH, use the SSH URL instead:

```
/plugin install trellis --marketplace ssh://git@git.vbmaf.net:2222/sebastien/trellis.git
```

The same thing in two steps, which is useful to know when you want to update it:

```
/plugin marketplace add <repository URL>
/plugin install trellis@trellis
/plugin marketplace update trellis
```

### Try it without installing

Clone the repository and point Claude Code at it for one session:

```sh
git clone <repository URL> trellis
claude --plugin-dir ./trellis
```

This is also how you test your own changes while working on the plugin.

### Check that it works

Run `/help` or type `/trellis:` and look for `trellis:incubate`. If you see it, the plugin is loaded.

## 2. The idea in one picture

Each phase writes a file in **your project's repository**; the next phase reads it. Files, not chat history, are the shared memory. You can stop for a month, come back, and nothing is lost.

```
idea ──► INCUBATION ──► DESIGN ───────────► DEVELOPMENT ────────► LIFE OF THE PROJECT
         SPEC.md        ARCHITECTURE.md     PLAN.md               STATUS.md
                        docs/adr/*.md       (deliverable slices)  CHANGELOG.md
```

Trellis is **strict but soft**. If a prerequisite is missing (for example, no validated spec before design), the skill says what is missing and what it risks, then lets you decide. When you go ahead anyway, the override is written down in the document's decision log, so the choice stays visible later.

## 3. What is available today

| Name | Kind | Phase | Status |
| --- | --- | --- | --- |
| `incubate` | skill | incubation | **available** |
| `architect` | skill | design | planned |
| `adr` | skill | design | planned |
| `slice` | skill | development | planned |
| `deliver` | skill | development | planned |
| `resume` | skill | any | planned |
| `devils-advocate` | agent | incubation, design | planned |
| `verifier` | agent | development | planned |

This page documents what exists. Planned items are described in the [README](../README.md) and will get their own section here when they ship.

## 4. Incubate an idea (`incubate`)

### Start

Open Claude Code **in the folder where the project will live** (an empty folder or an existing repository), then either describe your idea:

> I'd like to build a small tool that turns my commits into a monthly activity report. Can we incubate it?

or call the skill directly:

```
/trellis:incubate a small tool that turns my commits into a monthly activity report
```

Both work. The direct call is handy if Claude does not pick the skill up on its own.

### What happens

1. **Context.** Claude reads the folder (existing `SPEC.md`, README, notes) before asking anything, so it does not ask what is already written. If a `SPEC.md` exists, it updates it rather than starting over.
2. **Seed.** It restates your idea in two sentences. Correct it if it is wrong.
3. **Interview.** One question at a time, each with a suggested answer. Reply "ok" to accept it, or correct it. The topics, in order:
   problem and user, why this and why now, success criteria, scope and **non-goals**, constraints, existing solutions, riskiest assumption, cheapest test, kill criteria.
4. **Challenge.** Before writing, it tells you what worries it: scope creep, vague success criteria, an untested assumption, an existing tool that already does most of it.
5. **Draft.** It writes `SPEC.md` and a minimal `STATUS.md`.
6. **Verdict.** It ends with a recommendation:

| Verdict | Meaning |
| --- | --- |
| **Proceed** | The spec is solid; next step is design |
| **Test first** | The riskiest assumption must be checked first; the test is described |
| **Park** | Not now; what would reopen it is noted |
| **Abandon** | Not worth building, with the reason |

Abandoning or parking is a valid, useful result. It means the idea was cheap to kill.

### What you get

- **`SPEC.md`**: summary, problem, success criteria, scope (in and out), requirements, constraints, prior art, risks and assumptions, cheapest test, kill criteria, open questions, decision log.
- **`STATUS.md`**: the current phase, the next action, and where things stand.

Requirements carry stable IDs (`R1`, `R2`, ...), a priority (Must / Should / Could) and *how they will be verified*. Later phases trace against these IDs, so avoid renumbering them by hand.

### Tips

- **Short answers are fine.** "Ok", "yes", or a few words are enough; the skill proposes, you correct.
- **Say "I don't know".** Unknowns go to the open-questions or assumptions sections instead of being invented.
- **Size follows the idea.** A small personal tool gets a short interview and a one-page spec.
- **Override freely.** If the verdict is "test first" and you want to go on anyway, say so. It will be logged, not refused.
- **Come back later.** Run it again in the same folder to revise the spec; it works in update mode.

## 5. Languages

Names (skills, agents, files) are in English. Documents are generated in the language of your project: the language you write in, unless the project already has documents in another one. Templates exist in English and French (`skills/<name>/templates/*.en.md` and `*.fr.md`).

## 6. Troubleshooting

| Symptom | Likely cause and fix |
| --- | --- |
| `/trellis:incubate` is unknown | The plugin is not loaded. Check `/plugin`, or start Claude Code with `--plugin-dir`. Restart the session after installing. |
| Claude does not use the skill when you describe an idea | Call it explicitly with `/trellis:incubate`. |
| It asks things you already wrote down | Put the notes in the project folder (README, notes) before starting, so it reads them in step 1. |
| The interview feels too long | Say so. The skill sizes the interview to the idea and can skip topics you have already settled. |
| An update did not arrive | Run `/plugin marketplace update trellis`, then restart the session. |

## 7. Contributing and working on the plugin

Conventions for this repository (layout, naming, bilingual docs, "strict but soft") are in [`CLAUDE.md`](../CLAUDE.md). To check a change, run:

```sh
claude plugin validate .
```
