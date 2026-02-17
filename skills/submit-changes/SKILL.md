---
name: submit-changes
description: 'Guide the agent to submit code changes or prepare a release. Use when the user asks to commit, submit, push, tag, or release changes, or says 提交/送出/提交變更. The workflow always uses the same path: read the change range from the last version tag to HEAD, confirm version tag and semver bump, run edge-case-test-fixer then code-simplifier for code-affecting changes, then update version files, CHANGELOG, README (if needed), update AGENTS.md, commit, and push with tag.'
---

# Submit Changes

## Overview

Run a standardized submit workflow: inspect changes, read release scope from the last version tag to HEAD, confirm version tag and bump, run `edge-case-test-fixer`, run `code-simplifier` (for code-affecting changes), then update version files and CHANGELOG, update README if it contradicts changes, update AGENTS.md, commit, then push with tag.

## Dependency Contract (Required)

For code-affecting changes, always run this workflow with these two skills after version confirmation and before any release document edits:

1. `edge-case-test-fixer` (first)
2. `code-simplifier` (second)

If either dependency is unavailable, stop and report which dependency is missing.

If the change set is documentation-only and does not modify runtime code, tests, build scripts, or configuration behavior, you may skip both dependencies.

## References

Load these only when needed:

- references/semantic-versioning.md
- references/commit-messages.md
- references/branch-naming.md
- references/changelog-writing.md
- references/readme-writing.md

## Workflow

### 1) Inspect current changes

- Run `git status -sb`, `git diff --stat`, and `git diff --cached --stat`.
- Record whether there are staged files (`git diff --cached --name-only`).

### 2) Identify last version tag and change range

- Find the latest version tag (prefer semantic version tags) using `git tag --list` and `git describe --tags --abbrev=0`.
- If tags exist, set the range to `<last_tag>..HEAD`.
- If no tags exist, set the range to the initial commit (`git rev-list --max-parents=0 HEAD`)..`HEAD`.
- Review `git log --oneline <range>` and `git diff --stat <range>` to understand release scope.
- Always use this range as the source for release summary and changelog classification.

### 3) Ask for version tag and version bump

- Read current version from version files (prefer `project.toml` and `package.json` if present).
- Inspect existing tags to infer the tag format (for example, `vX.Y.Z` vs `X.Y.Z`).
- Infer a preferred semver bump from `<last_tag>..HEAD` change scope using references/semantic-versioning.md.
- Derive a preferred next version candidate from the current version and the preferred bump.
- Ask the user to choose both:
  - the version tag format or exact target version
  - the semver bump (`major`, `minor`, `patch`, or pre-release if the repo already uses it)
- In the same question, provide your recommendation as a reference:
  - preferred bump and preferred target version
  - a short reason tied to detected change type (breaking, feature, or fix)
- Keep final authority with the user; recommendation is guidance, not an override.
- If the user specifies a concrete version, use it.
- Follow references/semantic-versioning.md for bump rules.

### 4) Determine whether dependencies are required

- Inspect the release scope and staged diff to classify the change set:
  - `code-affecting`: includes runtime code, tests, build scripts, CI logic, or behavior-changing config.
  - `docs-only`: documentation/content updates only (for example `README.md`, `CHANGELOG.md`, docs).
- If `docs-only`, skip steps 5 and 6 and continue at step 7.

### 5) Run edge-case-test-fixer (required for code-affecting changes)

- After the version decision is confirmed, run `edge-case-test-fixer` against the current change set.
- Add targeted edge-case tests and apply minimal fixes only when tests expose real failures.
- Run the relevant tests and keep scope limited to the active changes.

### 6) Run code-simplifier (required for code-affecting changes)

- After edge-case hardening is complete, run `code-simplifier` on the recently modified code.
- Preserve behavior exactly; simplify only for clarity and maintainability.
- Re-run relevant tests if simplification touched runtime logic.

### 7) Update version files

- Update all detected version files consistently.
- Typical targets: `project.toml`, `package.json`, and any documented version file in the repo.
- Keep formatting intact; only change the version value.

### 8) Update CHANGELOG

- Update `CHANGELOG.md` with a new entry for the new version.
- Summarize changes based on the `<last_tag>..HEAD` (or initial commit..HEAD) range and classification.
- Follow references/changelog-writing.md for structure and tone.

### 9) Update README if needed

- Check whether README content contradicts the changes (features, configuration, usage, examples).
- If it does, update README to match the new behavior.
- Follow references/readme-writing.md for scope and tone.

### 10) Update AGENTS.md if needed

- Locate `AGENTS.md` at the repo root.
- Update it to reflect the changes and keep agent rules current.
- Treat this like README sync: update only when workflow instructions or agent rules are affected.
- If `AGENTS.md` does not exist, ask the user before creating it.

### 11) Commit and tag (do not push yet)

- Compose a concise commit message per references/commit-messages.md.
- Derive `type` and `subject` from the actual staged diff, not from the target version.
- Never use version-only commit subjects such as `feat: vX.Y.Z`.
- Use a release maintenance message (for example `chore(release): bump version and update changelog`) when staged changes contain only release metadata.
- Keep user intent from staging state:
  - if there are staged non-release files, include them with version/changelog/README/AGENTS.md updates.
  - if there are no staged non-release files, stage only version files, `CHANGELOG.md`, README updates (if any), and `AGENTS.md` (if updated).
- Create the version tag locally.

### 12) Push

- Push the commit and tag to the current branch.

## Notes

- Prefer direct actions; ask only for version choices if not provided.
- When asking for versioning, always include your preferred bump/version recommendation with brief reasoning.
- For code-affecting changes, do not edit `CHANGELOG.md`, README, or AGENTS.md before running `edge-case-test-fixer` then `code-simplifier`.
- Never guess versions; always read from files.
- If tests are required by the repo conventions, run them before commit.
- If a new branch is needed, follow references/branch-naming.md.
