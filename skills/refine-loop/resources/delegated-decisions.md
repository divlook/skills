# Delegated Decisions

Load this resource only after `SKILL.md` activates Delegated Decision Mode.

## Decision Rule

For each finding under the common User Decision Boundary, choose the smallest reasonable option that preserves:

- The stated goal and conversation context
- Repository conventions and artifact intent

Record the choice and its reason under `Delegated Decisions Applied`.

## Delegation Boundaries

Source mutation remains governed by the common Editing Policy.

Handle other boundaries by outcome:

- An unclear target or scope returns to Frame for clarification.
- An unavailable required target or resource ends the run as `BLOCKED`.
- A finding about these operations remains a user decision:
  - Secrets, credentials, permissions, billing, or security policy
  - Data deletion, migration, or persistent data transformation
  - Production deployment, external side effects, or operational resources
  - Commit, push, pull request creation, deployment, or another separately authorized operation

For a boundary-limited user decision, perform no boundary action.
Ask the user. End the current run as `FAIL`.
List the decision under both `Remaining Issues` and `User Decisions Needed`.
Complete unrelated safe review and proposal work before reporting.

Complete this branch only when you record every delegated decision and perform no boundary action.