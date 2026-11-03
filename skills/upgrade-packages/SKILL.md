---
name: upgrade-packages
description: Upgrade dependencies to latest, researching every release in between first.
disable-model-invocation: true
argument-hint: "[package ...]"
---

# Upgrade packages

Upgrade this project's dependencies to their latest versions. Research comes first: the changelogs, release notes, and docs for every release after the version we're on, up to and including the latest. That research makes the upgrade accurate and shows which new features we should adopt.

**Scope** is every direct dependency in every workspace and every ecosystem in the repo. If the user named packages, scope is those packages and their families.

## 1. Inventory

Find every manifest and lockfile; the lockfile decides the package manager. For each in-scope dependency, record:

- **current**: the version the lockfile resolves, not the manifest's specifier.
- **target**: the registry's `latest` tag. Stay off prereleases unless we're already on one. Type packages that track a runtime follow the runtime we actually run: `@types/node` matches the Node major in `.nvmrc`, `engines`, or CI, not npm's latest.
- **range**: every published version after current, up to and including target, taken from the registry's version list (`npm view <pkg> time --json` for npm, the equivalent elsewhere) so the research can account for each one.

Group dependencies into **families**: packages that move together because of peer dependencies or a shared release train (`react` + `react-dom` + `@types/react`; a framework and its plugins; one monorepo's scoped packages). A family is researched, upgraded, and held as a unit.

Done when every in-scope dependency has current, target, and range, or is already on target.

## 2. Baseline

If you're on the default branch, create `upgrade-packages-<YYYY-MM-DD>` and work there.

Run the checks the project defines (its scripts and CI: typecheck, lint, tests, build) and record which pass. That's the **baseline**: every check green now stays green through the upgrade. Checks already red are noted and left alone.

Done when you're on a working branch and every check has a recorded pass or fail.

## 3. Research

Every family with a non-empty range gets a **brief**, written by following [`RESEARCH.md`](RESEARCH.md). When your host has subagents, dispatch one per family in parallel (families with only patch releases can share one). Give each the absolute path to `RESEARCH.md`, the repo path, and its family's packages with current and target. Without subagents, read `RESEARCH.md` and write the briefs yourself.

Done when every family has a brief and every version in every range appears in one.

## 4. Plan

Turn the briefs into ordered **batches**:

1. Engine and toolchain changes the briefs require (Node, TypeScript, the package manager itself).
2. One batch for all families whose range brings nothing that touches our code beyond the version bump.
3. One batch per remaining family, dependencies before the families built on them, majors last.

Each batch lists its migration steps, codemods, adopt features, and removable workarounds. Save the plan and every brief to `upgrade-packages-<YYYY-MM-DD>.md` in the system temp directory, where the test and review steps read them. Post the plan in chat, then start upgrading. Stop for the user only when a migration forces a product decision the briefs can't settle, such as two replacement APIs that behave differently for users. Ask with your host's question tool (`AskUserQuestion` in Claude Code, `request_user_input` in Codex) so the thread shows it's waiting.

Done when every family with work is in a batch, the plan file is saved, and the plan is posted.

## 5. Upgrade

For each batch, in order:

1. Bump the manifests, keeping each specifier's style (`^`, `~`, exact), and update the lockfile with the project's package manager.
2. Run the codemods, then apply the migration steps at every call site the briefs cite.
3. Make the adopt changes and remove the workarounds the briefs mark as fixed upstream.
4. Run the baseline checks. Fix failures from the brief and the new version's docs, preferring docs shipped inside the installed package (`node_modules/<pkg>/docs` and the like) because they match the exact version.
5. Commit the batch.

A family that can't reach its target cleanly (a regression in latest, a peer that hasn't caught up) is **held** at the highest version that passes, with the reason recorded. Commit it at that version.

Done when every in-scope dependency is at target or held with a reason, and every baseline-green check is green.

## 6. Test locally

The checks prove the code builds and the tests pass; this step proves the app still works. Run it the way a developer does (the dev server, then the production build if the project has one) and exercise every path the upgrade touched: each silent change in the briefs, each adopted feature, the features behind every migrated call site, and the original problem behind each removed workaround. Watch the terminal and the browser console for new errors and deprecation warnings. For a library, exercise its public entry points the way a consumer would: its examples, or a short script against the built output.

Fix what breaks, re-run the baseline checks, and commit.

Done when every touched path has been exercised and works, or is recorded as not testable locally with the reason (needs production data, a third-party webhook, a deploy-only setting).

## 7. Review

Get the whole branch (`git diff <merge-base>...HEAD`) reviewed by reviewers who didn't do the upgrade: the `council` skill when it's installed, with the plan file as the spec; otherwise a fresh subagent given the diff and the plan file. The review covers:

- Every migration step in the briefs, applied at every cited call site, with no deprecated API left in use.
- Adopt changes and workaround removals: correct and complete.
- The lockfile: no duplicate versions within a family, no transitive majors nobody researched.
- The manifests: specifier styles kept, nothing outside scope bumped.

Fix each real finding about the upgrade, re-run the baseline checks, re-test any path the fix touches, and commit. Record a reason for every finding you reject. Findings about code the upgrade didn't change go in the report for later.

Done when every finding is fixed, rejected with a reason, or listed for later.

## 8. Report

Reply with:

- A table: family, from → to (or held, with reason), notable changes.
- Each adopted feature, with the files it changed.
- The **optional** features from the briefs, each with the files it would touch, so the user can pick them up later.
- What was tested locally and how, and what couldn't be tested locally, with the reason.
- Review findings: what was fixed, what was rejected and why, and what was left for later.
- Silent behavior changes, and anything needing a manual look outside the repo (env vars, deploy config, data migrations).
- Sources: the release notes and guides the briefs used.
