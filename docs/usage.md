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
idea ──► INCUBATION ──► DESIGN ─────────────────► DEVELOPMENT ────────► LIFE OF THE PROJECT
         SPEC.md        ARCHITECTURE.md + ADRs    PLAN.md               STATUS.md
                        DESIGN.md (UX, UI)        (deliverable slices)  CHANGELOG.md
                        TESTING.md
```

Trellis is **strict but soft**. If a prerequisite is missing (for example, no validated spec before design), the skill says what is missing and what it risks, then lets you decide. When you go ahead anyway, the override is written down in the document's decision log, so the choice stays visible later.

## 3. What is available today

| Name | Kind | Phase | Status |
| --- | --- | --- | --- |
| `incubate` | skill | incubation | **available** |
| `prior-art` | skill | incubation | **available** |
| `devils-advocate` | agent | incubation, design | **available** |
| `ux` | skill | design | **available** |
| `architect` | skill | design | **available** |
| `adr` | skill | design | **available** |
| `spike` | skill | design | **available** |
| `ui` | skill | design | **available** |
| `design-reviewer` | agent | design | **available** |
| `security-reviewer` | agent | design, release | **available** |
| `test-strategy` | skill | design | **available** |
| `bootstrap` | skill | development | **available** |
| `slice` | skill | development | **available** |
| `deliver` | skill | development | **available** |
| `verifier` | agent | development | **available** |
| `publish` | skill | release | **available** |
| `resume` | skill | any | **available** |
| `change-course` | skill | any | **available** |
| `retro` | skill | any | **available** |
| `project-overview` | skill | any | **available** |
| `coherence-checker` | agent | any | **available** |
| `SessionStart` hook | hook | any | **available** |

Every item has its own section below, followed by a suggested order of use.

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

## 8. Design the user experience (`ux`)

For any project with an interface. Run it after `incubate`, before or alongside `architect`:

```
/trellis:ux
```

It reads `SPEC.md` and works through: the users and their context, **testable UX goals**, the **journeys** (one per Must requirement, including the first run), the information structure, the **inventory of screens with all their states** (empty, loading, error, offline, first use...), low-fidelity ASCII wireframes for the critical screens, and the key interface texts. Everything cites requirement IDs, and it ends with a walk-through of each journey and a coverage check (every user-facing Must has a screen; every screen serves a requirement).

The result is the **UX part of `DESIGN.md`**. It says nothing about colors or frameworks. For a command-line tool or an API, it treats commands, output and error messages as the interface. A project with no interface at all is told so and skipped.

## 9. Define the visual system (`ui`)

Run it once the UX part exists and the platform is decided:

```
/trellis:ui
```

It names the platform conventions to follow, proposes a **small set of semantic tokens** (color with light and dark values, type, spacing, shape, motion) with contrast ratios computed and recorded, lists the **components with their states** (preferring native ones, justifying custom ones), and writes the accessibility rules (target size, text scaling, screen-reader labels, focus order, reduced motion). The result is the **UI part of `DESIGN.md`**.

If the platform has not been decided, it flags it and offers to produce platform-agnostic tokens.

## 10. Plan the tests (`test-strategy`)

Run it after the architecture (and the design, if there is an interface), before slicing:

```
/trellis:test-strategy
```

It identifies the risks that matter, picks a proportional mix of test levels, and builds a **verification matrix**: every requirement gets a concrete, observable check at a given level. It also covers test data (no personal data in the repository), environments, **quality gates** (what blocks, what only warns), non-functional and manual testing, and a **definition of done** for each slice. The result is `TESTING.md`.

If something is hard to test because of how the system is built, it reports it as a finding to bring back to `architect`. It does not write the tests; that happens during delivery.

## 11. Get a design critique (`design-reviewer`)

The design counterpart of `devils-advocate`: an independent, **read-only** agent.

> Use the design-reviewer agent on my DESIGN.md.

It checks journeys against requirements, missing states, step counts, error messages, vocabulary consistency, and, for the UI part, platform fit, tokens, contrast, accessibility and components. It reports blocking, important and minor findings with file and section, and says "no blocking issue" when that is true. It cannot run the app, so contrast ratios it did not verify are labelled "stated, not checked".

## 12. Survey what exists (`prior-art`)

```
/trellis:prior-art
```

A deeper, sourced version of the quick check in `incubate`. You agree on five to eight **criteria taken from your requirements**, then it looks at products, open-source projects, platform features and combinations of tools, evaluates each against the criteria (with links and the date checked, and vendor claims labelled as such), and ends with a verdict: **adopt, adapt, build or do nothing**. Written to `docs/prior-art.md`. "Adopt X instead" is a valid result.

## 13. Settle an unknown (`spike`)

```
/trellis:spike can a share extension write to the shared store in under 200 ms?
```

Frames **one question**, with success, failure and inconclusive criteria written **before** the experiment and a time box. It helps design the cheapest experiment, run it, and record the result in `docs/spikes/NNNN-title.md`, then feeds the verdict back into the ADR (a `proposed` decision becomes `accepted`, or is superseded), the architecture's risk table, the spec's kill criteria and the plan. Spike code is throwaway.

## 14. Scaffold the project (`bootstrap`)

```
/trellis:bootstrap
```

Run after architecture and test strategy. It takes the stack from your ADRs, shows its plan, then uses the stack's **official generator** to create the structure, adds `docs/`, `.gitignore`, a README stub and `CHANGELOG.md`, and writes the **project's own `CLAUDE.md`** (where each document is, the real build and test commands, the rules). It ends with a **walking skeleton** that builds and passes one test. It never overwrites existing files, and creating a remote, pushing or enabling CI waits for your agreement.

## 15. Plan the work (`slice`)

```
/trellis:slice
```

Cuts the work into `PLAN.md`: **vertical slices** (`SL0`, `SL1`...), each observable, testable and small, with acceptance criteria taken from the verification matrix, the requirements, journeys and screens it serves, a size (S / M / L) and dependencies. Slice 0 is the walking skeleton; risky things come early; every Must is covered by a slice (a coverage table checks it); error and empty states are planned too. Later slices stay coarse. Re-running it after a change keeps the IDs of done slices.

## 16. Build a slice (`deliver`)

```
/trellis:deliver SL1
```

or "deliver the next slice". For one slice: it restates the criteria, writes the **tests first** and watches them fail, implements the minimum to pass, runs the tests, lint and build (and exercises user-facing work for real), offers an independent check by the `verifier`, then updates `PLAN.md`, `STATUS.md`, `CHANGELOG.md` and the test matrix, and commits if you agreed. It reports deviations instead of hiding them, never weakens a test to make it pass, and stops after each slice. It asks once whether each slice should be committed on its own branch.

## 17. Verify independently (`verifier`)

An agent that runs the checks and judges each acceptance criterion as **verified, failed or unverifiable, with evidence**. It also checks that the tests would really catch a regression (no empty assertions, nothing skipped or weakened), conformance to the architecture and design, scope, and that the documents were updated. Verdict: Accept, Accept with notes, or Reject. It reports and never fixes. Ask for it directly ("use the verifier agent on SL2") or accept `deliver`'s offer.

## 18. Release (`publish`)

```
/trellis:publish
```

Pre-release checks (clean tree, planned slices done, full tests, release gates, changelog), a proposed version, release notes, a build from the release commit (including **Apple signing and notarisation**, app stores, packages or containers as your ADRs say), a **check of the built artifact in a clean environment**, then the release itself and a rollback plan. Every outward step (tag, upload, store submission, deploy, announcement) needs your explicit go.

## 19. Pick a project back up (`resume`)

```
/trellis:resume
```

Reads `STATUS.md`, `PLAN.md` and git, and answers in one screen: the phase, what is done, what is in progress (branch, uncommitted work), what is open or stale, and **one next action** with the exact command. It points out when the status document and the repository disagree. Read-only; it offers to refresh `STATUS.md` and does so only if you agree.

## 20. Change course (`change-course`)

```
/trellis:change-course drop the sync feature
```

For a new, modified or dropped requirement, a pivot or a technology change. It shows the **impact** across spec, ADRs, architecture, design, tests, plan and code, sizes the change and the cost of not changing, checks whether a kill criterion is touched, and proceeds on your decision. It then updates the documents **in dependency order** without renumbering anything: dropped requirements are marked `dropped`, changed decisions become new superseding ADRs.

## 21. Look back (`retro`)

```
/trellis:retro
```

For the end of a phase, a milestone, a release or the project. It gathers facts (slices planned versus done, spikes, decisions, changes), checks the result against the **original success and kill criteria**, asks for your own view, and ends with an explicit verdict: continue, pivot, park, abandon or done. Actions say where they will live. Written to `docs/retro/YYYY-MM-DD-title.md`.

## 22. See all your projects (`project-overview`)

```
/trellis:project-overview ~/projects
```

Reads each project folder (status, spec status, plan progress, git activity) and gives one comparable row per project with flags (stale, blocked, no status document, kill criterion near), then suggests what to **focus on, unblock and park**. Read-only; it writes a summary file only if you ask.

## 23. Automatic checks (`coherence-checker`, `security-reviewer`)

Two more read-only agents.

- **`coherence-checker`** checks that documents and code still agree: requirement IDs and coverage across the documents, ADR statuses and index, `STATUS.md` freshness, components, stack and interface code against the architecture and design, tests against the verification matrix. It says which side is probably right. Run it every few slices, after a change of course, and before a release.
- **`security-reviewer`** builds a lightweight **threat model** (assets, actors, trust boundaries, entry points) and checks personal data, secrets, authentication, input handling, storage and transport, dependencies and the release chain, with ranked findings and mitigations. It is a first pass, not a professional audit or legal advice.

## 24. Session status hook

When you open a session in a project that has a `STATUS.md`, Trellis adds its first lines to the context so Claude knows where the project stands (the file's content is treated as data, not instructions). It stays silent when there is no `STATUS.md`. Set the environment variable `TRELLIS_NO_STATUS=1` to turn it off.

## 25. Suggested order

```
incubate ─► (prior-art) ─► (devils-advocate) ─► ux ─► architect ─► (spike) ─► ui
   ─► (design-reviewer, security-reviewer) ─► test-strategy ─► bootstrap ─► slice
   ─► deliver ⟲ (verifier) ─► publish
```

At any time: `resume`, `change-course`, `retro`, `project-overview`, `coherence-checker`, `adr`. Steps in parentheses are optional. Skip what does not apply (no interface: skip `ux`, `ui` and `design-reviewer`).

## 26. Languages

Names (skills, agents, files) are in English. Documents are generated in the language of your project: the language you write in, unless the project already has documents in another one. Templates exist in English and French (`skills/<name>/templates/*.en.md` and `*.fr.md`).

## 27. Troubleshooting

| Symptom | Likely cause and fix |
| --- | --- |
| `/trellis:incubate` is unknown | The plugin is not loaded. Check `/plugin`, or start Claude Code with `--plugin-dir`. Restart the session after installing. |
| Claude does not use the skill when you describe an idea | Call it explicitly with `/trellis:incubate`. |
| It asks things you already wrote down | Put the notes in the project folder (README, notes) before starting, so it reads them in step 1. |
| The interview feels too long | Say so. The skill sizes the interview to the idea and can skip topics you have already settled. |
| `Invalid marketplace source format` | You passed an `ssh://…` URL. Use `git@host:owner/repo.git` instead (see section 1). |
| `unable to get password from user` | You used the HTTPS URL on a private repository. Use the SSH form. |
| The project status appears at the start of every session and I do not want it | Set the environment variable `TRELLIS_NO_STATUS=1`. |
| An update did not arrive | Run `/plugin marketplace update trellis`, then restart the session. |

## 28. Contributing and working on the plugin

Conventions for this repository (layout, naming, bilingual docs, "strict but soft") are in [`CLAUDE.md`](../CLAUDE.md). To check a change, run:

```sh
claude plugin validate .
```
