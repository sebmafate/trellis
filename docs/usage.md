# Using Trellis

*Français : [usage.fr.md](usage.fr.md)*

Trellis is a [Claude Code](https://claude.com/claude-code) plugin. It adds skills (and, later, agents) that guide a project from a raw idea to shipped software. This page explains how to install it and how to work with it.

## 1. Install

You need Claude Code. Then install the plugin from this repository's marketplace:

```
/plugin install trellis --marketplace https://git.vbmaf.net/sebastien/trellis.git
```

While the repository is private, the HTTPS URL fails (Git cannot ask for a password in this context). Use the SSH form instead:

```
/plugin install trellis --marketplace git@git.vbmaf.net:sebastien/trellis.git
```

Write it in this `git@host:path` form. Claude Code **rejects `ssh://…` URLs** ("Invalid marketplace source format"). That form has no room for a port, so a non-standard SSH port must be set in `~/.ssh/config` (here: `Host git.vbmaf.net` with `Port 2222`).

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
| `architect` | skill | design | **available** |
| `adr` | skill | design | **available** |
| `slice` | skill | development | planned |
| `deliver` | skill | development | planned |
| `resume` | skill | any | planned |
| `devils-advocate` | agent | incubation, design | **available** |
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

## 5. Design the architecture (`architect`)

Run it in the same folder, once `SPEC.md` exists:

```
/trellis:architect
```

or just ask: "What's the right architecture for this?"

1. **Context.** It reads `SPEC.md` and `STATUS.md`. If the spec is missing or not validated, it warns you of the risk and offers to incubate first; you can continue anyway, and the assumptions it makes are listed.
2. **Decision drivers.** From your Must requirements it proposes the three to five qualities that decide trade-offs (privacy, offline use, cost to run, maintainability by one person...). You adjust the ranking.
3. **The list of decisions.** Platform, language, storage, hosting, authentication, testing, release... It separates what deserves an ADR (hard to reverse) from what is a one-line note.
4. **One decision at a time.** Two to four real options, compared on *your* drivers, with a recommendation and what it is least sure about. If a key fact is unknown, it proposes a **spike** (a small time-boxed experiment) and records the decision as `proposed` until it is done. You decide; it records.
5. **`ARCHITECTURE.md`.** Context, drivers, overview with a Mermaid diagram, components, data, key flows, cross-cutting concerns, a decisions index, and a **traceability table** (requirement → components → ADRs).
6. **Coverage check.** Every Must must land on a component; every component must serve a requirement. Gaps are reported plainly.

Tips: ask for the default it would take if you are stuck; say "this is a weekend tool" to keep the output short; run it again later to revise the design (accepted ADRs are superseded, never rewritten).

## 6. Record a decision (`adr`)

`architect` uses it automatically, but you can call it on its own whenever you make a technical choice:

```
/trellis:adr we chose SQLite over Postgres because the app is single-user and offline
```

It writes `docs/adr/NNNN-short-title.md` (numbered, one decision per file) with the context, **the options considered and why the others were rejected**, the decision, the consequences (including the negative ones), and a measurable trigger to revisit it. It also keeps `docs/adr/README.md` (the index) and the decisions table of `ARCHITECTURE.md` in sync.

An accepted ADR is never edited. When a decision changes, ask for a new ADR that **supersedes** the old one: only the old one's status line changes. ADRs are never deleted or renumbered.

## 7. Get a second opinion (`devils-advocate`)

A skeptical reviewer that runs as a separate agent, so its opinion is not anchored on the conversation that produced the document. It is **read-only**: it reports, it never edits.

Ask for it in plain words, for example:

> Use the devils-advocate agent to review my SPEC.md.

> Run trellis:devils-advocate on the architecture and the ADRs.

It reads `SPEC.md`, `ARCHITECTURE.md`, `docs/adr/` and `STATUS.md`, and reports: an overall judgement, **blocking** problems, **important** ones, minor ones, the two or three questions you must answer, and what is solid. Every finding cites the file and section. It scales to the project (a weekend tool is not held to enterprise standards) and says "no blocking issue" when that is true.

Good moments to call it: right after `incubate` (before investing), and after `architect` (before slicing the work).

## 8. Languages

Names (skills, agents, files) are in English. Documents are generated in the language of your project: the language you write in, unless the project already has documents in another one. Templates exist in English and French (`skills/<name>/templates/*.en.md` and `*.fr.md`).

## 9. Troubleshooting

| Symptom | Likely cause and fix |
| --- | --- |
| `/trellis:incubate` is unknown | The plugin is not loaded. Check `/plugin`, or start Claude Code with `--plugin-dir`. Restart the session after installing. |
| Claude does not use the skill when you describe an idea | Call it explicitly with `/trellis:incubate`. |
| It asks things you already wrote down | Put the notes in the project folder (README, notes) before starting, so it reads them in step 1. |
| The interview feels too long | Say so. The skill sizes the interview to the idea and can skip topics you have already settled. |
| `Invalid marketplace source format` | You passed an `ssh://…` URL. Use `git@host:owner/repo.git` instead (see section 1). |
| `unable to get password from user` | You used the HTTPS URL on a private repository. Use the SSH form. |
| An update did not arrive | Run `/plugin marketplace update trellis`, then restart the session. |

## 10. Contributing and working on the plugin

Conventions for this repository (layout, naming, bilingual docs, "strict but soft") are in [`CLAUDE.md`](../CLAUDE.md). To check a change, run:

```sh
claude plugin validate .
```
