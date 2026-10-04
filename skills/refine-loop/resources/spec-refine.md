# Spec Review

Use this resource for requirements, design specs, task specs, acceptance criteria, change proposals, and spec deltas.
Also use it for OpenSpec or Spec Kit artifacts.

## Criteria

Check that:

- The problem, intended outcome, scope, and non-goals agree.
- Terminology is consistent across requirements, design, tasks, and migration notes.
- Every requirement and acceptance criterion is observable and testable.
- Design decisions trace to a requirement and rationale.
- Tasks cover every required behavior without adding unapproved scope.
- Assumptions, constraints, dependencies, and open questions are visible.
- Open questions remain distinct from decided behavior.
- Current, proposed, compatibility, rollout, deprecation, and migration behavior do not contradict one another when relevant.

For OpenSpec, Spec Kit, or speckit targets, also check that:

- Proposal, requirement, design, task, and delta artifacts describe the same scope.
- Requirement deltas are specific enough to validate.
- Acceptance criteria map to requirements rather than only implementation details.
- Terminology follows the surrounding spec set.

`PASS` requires every requirement to trace to validation.
Every design or task decision must remain within approved scope.
