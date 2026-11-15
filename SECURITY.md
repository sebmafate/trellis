# Security policy

*Français : [SECURITY.fr.md](SECURITY.fr.md)*

Trellis is a [Claude Code](https://claude.com/claude-code) plugin made of Markdown instructions (skills and agents), templates, and one small shell script (the `SessionStart` hook in `hooks/`). There is no server and no network service. Even so, a plugin's instructions run with the permissions you give your Claude Code session, so security matters here.

## Supported versions

Only the **latest release** receives fixes. Please update (`claude plugin marketplace update trellis`, then `claude plugin update trellis@trellis`) before reporting, to check the issue still exists.

## Reporting a vulnerability

**Please do not open a public issue or pull request for a security problem.**

Use GitHub's private reporting instead: go to the repository's **Security** tab and choose **Report a vulnerability**, or open
<https://github.com/sebmafate/trellis/security/advisories/new>.

Helpful details:

- the affected file (a skill, an agent, the hook script, a template) and version;
- what an attacker could make the plugin do, and under which conditions;
- steps to reproduce, or a short description of the scenario;
- any suggested fix.

This is a personal project maintained by one person. I will do my best to acknowledge a report within about a week and to keep you informed. I cannot promise a fixed delay or a bounty. Please give a reasonable time to fix the issue before disclosing it publicly; you will be credited in the advisory if you wish.

## What is in scope

- The **hook script** (`hooks/session-status.sh`): command injection, unsafe handling of file content, anything that could run unintended code or leak data.
- **Skills and agents that act outside the conversation**, for example `autopilot` (push, pull requests), `publish` (tags, uploads, deployments), `developer` and `deliver` (file changes and commits), `bootstrap`: any way they could act without the confirmation they promise, push to or merge into a protected branch, expose secrets, or write outside the project.
- **Instructions that can be turned against the user**: for example, content read from a project's files (a `STATUS.md`, a brief, a report) that could make an agent follow an attacker's instructions with real consequences.
- **The repository itself**: leaked credentials, a compromised release, or a malicious change to the marketplace manifest.

## What is out of scope

- Vulnerabilities in **Claude Code**, the models, or the Anthropic services: please report them to Anthropic.
- General model behavior (such as an occasional wrong answer) that does not involve a Trellis instruction or script.
- Issues that need an already compromised machine or account.
- Findings about **your own project's code** produced by the `security-reviewer` agent; those belong to your project.

## Good practice when using Trellis

- Read a plugin's skills and agents before installing them, as you would any code you let act on your behalf.
- Keep Claude Code's permission prompts on for push, publish and delete operations. `autopilot` and `publish` ask for explicit authorisation for outward actions; do not pre-authorise more than you need.
- Never put secrets in the documents Trellis writes (`SPEC.md`, `STATUS.md`, briefs, reports). They end up in git history.
