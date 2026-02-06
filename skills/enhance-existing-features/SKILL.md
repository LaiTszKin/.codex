---
name: enhance-existing-features
description: Build and extend brownfield features in an existing codebase. Use when a request requires understanding module dependencies, researching the latest official docs for current tech stacks/APIs/external dependencies, implementing changes, and adding/adjusting tests in a mature project.
---

# Enhance Existing Features

## Overview

Extend existing systems safely by mapping dependencies first, verifying the latest authoritative docs, implementing minimal changes, and updating tests that match the scenario.

## Workflow

### 1) Explore codebase and map dependencies

- Locate entrypoints, configuration, and primary data flow.
- Trace module relationships (imports, call graph, shared models, side effects).
- Identify integration points (DB, RPC, external APIs, queues, filesystems).
- Summarize findings in working notes before editing.

### 2) Verify latest authoritative docs

- Identify the tech stack, libraries, and external dependencies involved.
- Use official documentation as the source of truth.
- Prefer Context7 for library/framework APIs; use web search for latest authorized docs and public APIs.
- If required docs are private or missing, request access or user-provided references before proceeding.

### 3) Implement the feature

- Reuse existing patterns and abstractions; avoid over-engineering.
- Keep changes focused and minimal; preserve current behavior unless required.
- Follow project conventions (naming, linting, formatting, configuration).
- Update configuration or environment examples only if new inputs are required.

### 4) Update/add test cases

- Choose the appropriate test type(s) for the scenario.
- Add tests near existing suites and follow current fixtures/conventions.
- Update mocks/stubs/fixtures to reflect the new behavior.
- Run relevant tests when possible and fix failures.

## Test References

Open the following references based on the testing scenario:

- `references/unit-tests.md` for isolated logic and boundary conditions.
- `references/property-based-tests.md` for invariants and broad input coverage.
- `references/integration-tests.md` for multi-module or external-system interactions.
- `references/e2e-tests.md` for full user or system flows.
