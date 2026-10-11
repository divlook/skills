# Delegated Decisions

Read these rules only after [Decision Delegation](../SKILL.md#decision-delegation) activates Delegated Decision Mode.

## Decision Rule

For each item within the common User Decision Boundary, choose the smallest reasonable option that preserves:

- The stated goal and conversation context
- Repository conventions and target intent

Record the choice and its reason under `Delegated Decisions Applied` before implementation.

## Delegation Boundaries

Source and candidate changes remain governed by the common Editing Policy.
An unclear target or scope requires clarification at Frame.
Unavailable required content uses the common Stop Conditions.

Keep these operations as user decisions:

- Secrets, credentials, permissions, billing, or security policy
- Data deletion, migration, or persistent data transformation
- Production deployment, external side effects, or operational resources
- Commit, push, pull request creation, deployment, or another separately authorized operation

For a boundary-limited decision, perform no boundary action under this delegation.
Ask under the common Waiting for Decisions rules.
Complete unrelated safe work before waiting when the editing mode and iteration limit permit it.
A user's answer may resolve the artifact decision without authorizing the underlying operation.
Require separate, explicit authorization for that operation.

Complete this branch when every delegated choice is recorded and every boundary-limited choice has explicit user resolution.
