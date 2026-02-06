---
name: submit-changes
description: Guide the agent to submit code changes or prepare a release. Use when the user asks to commit, submit, push, tag, or release changes, or says "提交"/"送出"/"提交變更". The workflow asks the user to choose release-only vs commit staged changes, confirms the version tag and semver bump, updates version files, CHANGELOG, README (if needed), commits, updates AGENTS.md, then pushes with tag.
---

# Submit Changes

## Overview

Run a standardized submit workflow: inspect changes, ask the user to choose a submit mode, confirm version tag and bump, update version files and CHANGELOG, update README if it contradicts changes, commit, update AGENTS.md, then push with tag.

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

### 2) Ask for submit mode

- Ask the user to choose exactly one:
  - release-only
  - commit the current staged changes
- If the user selects "commit the current staged changes" but there are none, ask them to stage files or switch to release-only.
- If the user selects release-only, do not stage or include any existing unstaged changes.

### 3) Identify last version tag and change range

- Find the latest version tag (prefer semantic version tags) using `git tag --list` and `git describe --tags --abbrev=0`.
- If tags exist, set the range to `<last_tag>..HEAD`.
- If no tags exist, set the range to the initial commit (`git rev-list --max-parents=0 HEAD`)..`HEAD`.
- Review `git log --oneline <range>` and `git diff --stat <range>` to understand release scope.

### 4) Ask for version tag and version bump

- Read current version from version files (prefer `project.toml` and `package.json` if present).
- Inspect existing tags to infer the tag format (for example, `vX.Y.Z` vs `X.Y.Z`).
- Ask the user to choose both:
  - the version tag format or exact target version
  - the semver bump (`major`, `minor`, `patch`, or pre-release if the repo already uses it)
- If the user specifies a concrete version, use it.
- Follow references/semantic-versioning.md for bump rules.

### 5) Update version files

- Update all detected version files consistently.
- Typical targets: `project.toml`, `package.json`, and any documented version file in the repo.
- Keep formatting intact; only change the version value.

### 6) Update CHANGELOG

- Update `CHANGELOG.md` with a new entry for the new version.
- Summarize changes based on the `<last_tag>..HEAD` (or initial commit..HEAD) range and classification.
- Follow references/changelog-writing.md for structure and tone.

### 7) Update README if needed

- Check whether README content contradicts the changes (features, configuration, usage, examples).
- If it does, update README to match the new behavior.
- Follow references/readme-writing.md for scope and tone.

### 8) Commit and tag (do not push yet)

- Compose a concise commit message per references/commit-messages.md.
- If in release-only mode, stage only version files, `CHANGELOG.md`, and README updates (if any).
- If in commit-staged mode, include the staged changes plus version/changelog/README updates if applicable.
- Create the version tag locally.

### 9) Update AGENTS.md after the commit

- Locate `AGENTS.md` at the repo root.
- Update it to reflect the changes and keep agent rules current.
- If `AGENTS.md` does not exist, ask the user before creating it.
- If the commit has not been pushed yet, amend the previous commit to include the `AGENTS.md` update.
- If the commit was already pushed, create a follow-up commit and push (avoid force-push unless the user explicitly asks).

### 10) Push

- Push the commit and tag to the current branch.

## Notes

- Prefer direct actions; ask only for the submit mode and version choices if not provided.
- Never guess versions; always read from files.
- If tests are required by the repo conventions, run them before commit.
- If a new branch is needed, follow references/branch-naming.md.
