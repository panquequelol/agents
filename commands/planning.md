Create an implementation plan for the user task. Stop after the plan and wait for approval.

## Boundaries

1. Read `AGENTS.md` and relevant project documentation.
2. State assumptions. Ask one focused question only when its answer changes the scope or architecture.

## Design and slices

1. Define the outcome, non-goals, constraints, and terms for the task.
2. Choose the smallest durable design that fits the existing architecture and known requirements.
3. Use existing project patterns before adding abstractions. Use a maintained library when it reduces total complexity and fits the project.
4. Compare options only when they have materially different effects. Choose one approach.
5. Study established products only for unfamiliar user-facing patterns or unresolved design decisions.
6. Split the work into ordered vertical slices. Each slice must produce one observable result, remain independently verifiable, and leave the repository in a valid state.
7. Put foundation work in the first slice that uses it. Add a separate foundation slice only when no behavior slice can verify it.
8. For each slice, list the scenario, files and symbols, changes, behavior to preserve, dependencies, checks, and stop conditions.

## Output

Use only these sections:

1. `## Goal and scope`
2. `## Design`
3. `## Vertical slices`
4. `## Risks and open questions`

For `## Goal and scope`, include the outcome, non-goals, constraints, and assumptions.

For `## Design`, include the chosen approach, existing patterns, affected files or modules, and downstream contracts.

For `## Vertical slices`, number the slices. For each slice, include:

- Result
- Scenario
- Changes
- Preserve
- Verify
- Depends on
- Stop if

For `## Risks and open questions`, include only unresolved risks, blocking questions, and their guardrails. Write `None` when there are none.

## Rules

- Make architectural decisions for the long term. Design for known requirements and verified extension points.
- Do not accept a stopgap that only works for now and is meant to be replaced later.
- Keep each slice small enough to implement and verify in one focused session.
- Do not invent requirements, files, symbols, or extension points. Mark unverified details as assumptions.
