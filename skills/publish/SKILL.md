---
name: publish
description: 'Prepare and perform a release: check that the planned slices are done and verified, choose the version, finalise CHANGELOG and release notes, build the artifacts for the target platform (including Apple signing and notarisation, app stores, packages, containers, CLIs), verify the result on a clean install, tag, publish and plan the rollback. Use when the user wants to release, ship, publish, deploy, cut a version or distribute the project. Every outward-facing step needs explicit confirmation.'
---

# Publish

Turn a verified state of the project into a **released version**, safely and reproducibly. Publishing is outward-facing and hard to undo, so each outward step waits for explicit agreement.

## Principles

- **Release what was verified.** Only slices that are `done` and verified go into a release. Say what is in and what is not.
- **Reproducible.** The release is built by documented commands from a clean checkout of a tag, not from a developer's dirty working tree.
- **Confirm every outward step.** Pushing a tag, uploading an artifact, submitting to a store, deploying, announcing: ask before each, and say what will happen and whether it can be undone. Approval for one step does not cover the next.
- **Verify the artifact, not the source.** Install or run the built artifact on a clean environment before calling it released.
- **Plan the way back.** Know how to roll back or withdraw the release before you publish it.
- **No secrets in the repository or the logs.** Signing keys and tokens come from the environment or a keychain, never from files in the repo.
- **Soft gates.** Flag a missing prerequisite and its risk, then let the user decide.

## Steps

### 0. Read the context

Read `PLAN.md`, `STATUS.md`, `CHANGELOG.md`, `TESTING.md` (release gates), `ARCHITECTURE.md` and the ADRs (distribution and hosting decisions: app store or direct download, package registry, container registry, deployment target), and the project's `CLAUDE.md` (build commands). Look at the git state.

### 1. Pre-release checks

Report each as pass, fail or not checked, and flag failures without blocking (the user decides):

- Working tree clean, on the intended branch, up to date with the remote.
- The slices planned for this release are `done`; none `in progress`.
- Full test suite, linter and build pass (run them; say what you ran).
- The release gates in `TESTING.md` are met.
- `CHANGELOG.md` "Unreleased" is accurate; no known blocking issue in the plan.
- Licence, README and the documentation match the behavior.
- A quick security and privacy look: no secrets, dependencies reviewed. The `security-reviewer` agent can do the deep version.
- Open decisions that touch the release (distribution, signing, entitlements).

### 2. Choose the version

Propose a version following the project's scheme (default: Semantic Versioning). Explain the choice from the changes (breaking, feature, fix). Get agreement.

### 3. Finalise the release notes

Move "Unreleased" under the new version and date in `CHANGELOG.md`; write release notes for users (what changed, upgrade steps, known issues). Show the diff before committing.

### 4. Build the artifacts

Build from the release commit with the documented commands. Per target, as the decisions say:

- **Apple platforms**: version and build numbers, signing identity and provisioning, hardened runtime and entitlements, **notarisation** and stapling for direct distribution, or archive and upload for TestFlight and the App Store. Check current requirements before acting.
- **Packages and libraries**: build, inspect the package contents, publish to the registry (or a dry run first).
- **Containers and services**: build and tag the image, scan it, push, deploy to the target environment with the rollout strategy the architecture names.
- **CLIs and plugins**: build per platform, checksums, a package manager formula if applicable.

### 5. Verify the artifact

Install or run the built artifact in a clean environment (fresh user, fresh VM or container, another machine) and run the smoke checks from `TESTING.md`: it starts, the critical journey works, it upgrades from the previous version if relevant.

### 6. Publish (with confirmation)

Ask for explicit go before each of: committing the release changes, creating and pushing the tag, uploading artifacts, creating the release on the forge, submitting to a store, deploying, and announcing. For each, state what happens and whether it is reversible.

### 7. After the release

Record the release in `STATUS.md`, note the rollback procedure used or ready, and list what to watch (errors, feedback, crashes) for the first days. Suggest `retro` at a milestone, and `deliver` for what comes next.

## What not to do

- Do not publish, push tags or deploy without an explicit go.
- Do not build from a dirty tree or an unverified commit.
- Do not skip the clean-environment check.
- Do not put keys or tokens in files, commands logged to history, or the repository.
- Do not claim a platform requirement from memory when it can be checked.
