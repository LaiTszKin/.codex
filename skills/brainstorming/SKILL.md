---
name: brainstorming
description: Clarify ambiguous or uncertain user requests by asking targeted questions (3-5 initially) to elicit requirements and desired business logic, then summarize the clarified requirement. Use when the user is unsure, questioning a function, or provides an unclear description of expected behavior or core logic.
---

# Brainstorming

## Overview

Use this skill to turn unclear or hesitant requests into a crisp, validated requirement focused on business logic, not implementation.

## Workflow

1. Detect ambiguity or uncertainty in the user's request.
2. Ask 3-5 focused questions to surface intent, inputs, outputs, rules, and constraints.
3. If critical gaps remain, ask follow-up questions (may exceed 5 total) until core logic is clear.
4. Summarize the clarified requirement in a concise, structured form and ask for confirmation.

## Question design guidelines

- Keep questions short and concrete.
- Prefer open-ended or choice-based questions over yes/no.
- Anchor on business logic: goal, actors, inputs, outputs, rules, constraints, and success criteria.
- Avoid proposing implementation details or solutions before requirements are clear.
- Match the user's language and terminology.

## Question prompts (pick the most relevant)

- Goal: What outcome should this function or feature achieve?
- Scope: What is in scope, and what should be explicitly out of scope?
- Inputs: What inputs or triggers start the behavior?
- Outputs: What should the output or side effects be?
- Rules: What rules or conditions govern the behavior?
- Edge cases: What should happen in failure or boundary cases?
- Constraints: Any performance, security, or compliance constraints?
- Dependencies: Does it interact with other systems or data sources?
- Success: How will we know it works as intended?

## Output format

Provide a clarified requirement that describes the core logic. Use a short paragraph or 4-6 bullets covering goal, inputs, outputs, key rules, and constraints. End with a brief confirmation prompt.
