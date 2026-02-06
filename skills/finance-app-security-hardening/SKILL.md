---
name: finance-app-security-hardening
description: Analyze financial applications with a red-team attacker mindset, prioritize and verify security risks with concrete evidence, implement minimal safe fixes, and write regression/security test cases. Use when users ask to audit fintech/DeFi/payment/accounting systems, pressure-test extreme attack scenarios, or remediate vulnerabilities with tests.
---

# Finance App Security Hardening

## Overview

Use this skill to harden financial applications with an evidence-first red-team workflow: think like a determined attacker, prove exploitable paths with tests, apply minimal fixes, and verify no money-handling regressions.

## Workflow

### 1) Adopt red-team objective and attack model

- Assume the attacker is rational, patient, and capital-efficient.
- Start from attacker goals: steal funds, mint value, bypass limits, freeze settlement, or corrupt accounting state.
- Define attacker capabilities: repeated requests, concurrency/race timing, crafted payloads, stale/manipulated external data, and permission boundary probing.
- Open `references/red-team-extreme-scenarios.md` and select relevant attack paths before code changes.

### 2) Define scope and trust boundaries

- Identify money-critical entry points (API handlers, jobs, contract calls, settlement logic).
- List protected assets and invariants (balance conservation, non-negative balances, authorization constraints, idempotency).
- Record trust boundaries (user input, external APIs, price feeds/oracles, admin actions, cross-service messages).

### 3) Investigate risks with code evidence

- Open `references/risk-checklist.md` and run checks relevant to the code path.
- Keep only risks supported by concrete evidence (`file:line`, data flow, or reproducible behavior).
- For each candidate risk, capture exploit path, affected asset, and likely impact.
- Avoid speculative findings that cannot be verified in code or tests.

### 4) Prioritize and plan remediation

- Score each confirmed risk with Impact (1-5) x Exploitability (1-5).
- Prioritize Critical/High risks first, then Medium, then Low.
- Define a test plan for each risk before editing implementation.

### 5) Write failing attack tests first

- Open `references/security-test-patterns.md` and select test patterns matching the risk.
- Add at least one failing exploit-path test per risk before applying fixes.
- Include extreme cases when relevant: concurrent duplicate actions, precision boundaries, and stale or malicious external inputs.
- Prefer deterministic tests; isolate external systems with stubs/mocks unless integration behavior is the risk itself.

### 6) Implement minimal safe fixes

- Patch the root cause with the smallest possible change set.
- Preserve existing public contracts unless change is required for security.
- Add guard clauses, invariant checks, and error handling where violations originate.
- Avoid unrelated refactors while remediating security issues.

### 7) Verify and report

- Run targeted tests first, then relevant suite checks (lint/type/integration as available).
- Confirm each previously failing security test now passes.
- Report residual risks, assumptions, or deferred hardening tasks.

## Output format

Use this structure in responses:

1. Findings (sorted by severity)
   - Risk title
   - Evidence: `path:line`
   - Attack path and exploit preconditions
   - Impacted asset/invariant
2. Remediation
   - Code change summary
   - Why the fix blocks exploitation
3. Test cases written
   - New/updated test names and paths
   - What each test proves
4. Validation
   - Commands executed
   - Key pass/fail results
5. Residual risk or follow-ups

## Resources

- `references/red-team-extreme-scenarios.md`: Red-team attacker goals, capabilities, and extreme attack scenarios.
- `references/risk-checklist.md`: Financial application security checklist and severity guidance.
- `references/security-test-patterns.md`: Practical attack-driven test patterns for auth, fund safety, replay/race, precision, and external dependency risks.
