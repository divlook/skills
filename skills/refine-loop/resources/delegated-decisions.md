# Delegated Decisions

Load this resource only after `SKILL.md` activates Delegated Decision Mode.

## Decision Rule

For each finding governed by the common User Decision Boundary, choose the smallest reasonable option that preserves the stated goal, conversation context, repository conventions, and artifact intent. Record the choice and its reason under `Delegated Decisions Applied`.

## Delegation Boundaries

Source mutation remains governed by the common Editing Policy.

Handle other boundaries by outcome:

- An unclear target or scope returns to Frame for clarification.
- An unavailable required target or resource ends the run as `BLOCKED`.
- A finding involving secrets, credentials, permissions, billing, security policy, data deletion, migration, persistent data transformation, production deployment, external side effects, operational resources, commit, push, pull request creation, deployment, or another separately authorized operation remains a user decision.

For a boundary-limited user decision, perform no boundary action, ask the user, and end the current run as `FAIL`. List it under both `Remaining Issues` and `User Decisions Needed`. Complete unrelated safe review and proposal work before reporting.

This branch is resolved only when every delegated decision is recorded and no boundary action was performed.