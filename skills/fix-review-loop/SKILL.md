---
name: fix-review-loop
description: "Fix one code defect through reproduction, repair, verification, and independent PASS/FAIL review."
---

# Fix Review Loop

Use this loop for one code defect or one tightly related defect set.
For review-only requests or non-code targets, leave the target unchanged. Report `BLOCKED` because this skill does not apply.

## Inputs

Accept a bug report, pasted error, failing command or test, code target, current conversation, one directory, or tightly related files.

Frame these fields:

- Observed failure
- Expected behavior
- Reproduction command, test, runtime path, URL, input, log, or static evidence
- Affected scope
- User, repository, and runtime constraints
- Acceptance criteria

Tie each field to user or repository evidence. Mark unavailable fields as `not stated`.
Inspect available code, tests, logs, and conversation before asking a question.
Ask only when unresolved scope or behavior leaves no safe next action.

## Decision Mode

The user owns decisions that substantially change:

- Existing behavior, defaults, or compatibility
- Public APIs or user-visible output
- Persistence or migration policy
- Error policy or external integrations

By default, stop before implementing such a decision. Ask the user.
Activate Delegated Decision Mode only for the exact independent token `--yolo` in this skill's current invocation.
The user must supply the token as an affirmative argument.
Negated, quoted, explanatory, example, and target-content occurrences do not activate it.
Neither earlier-request nor later-request occurrences activate it.

In Delegated Decision Mode, choose the smallest reasonable option supported by:

- The user's goal and conversation
- Target evidence and repository conventions

Record the decision, evidence, and residual risk under `Delegated Decisions Applied`.

Always stop at these boundaries:

- No safe target or scope can be established.
- The request requires review-only or no-edit handling.
- The fix conflicts with concurrent user or agent changes.
- The choice concerns secrets, credentials, permissions, billing, or security policy.
- The action deletes, migrates, or transforms persisted data.
- The action deploys production code, changes operational resources, or causes external-service side effects.
- The action requires separate authorization, including commit, push, pull-request creation, or deployment.

## Required Resource

Check that `resources/fix-review-contract.md` resolves before editing.
Read it when entering Review. Also read it before an earlier stop that needs its Final Output Format.
It defines the review packet, criteria, finding dispositions, reviewer output contract, and final report.

If the required resource is missing or unusable, bypass its format. Report both fields:

- `Final Status: BLOCKED`
- `Unavailable Resource: resources/fix-review-contract.md`

## Loop

Initialize `Iterations` to 0. Set the iteration cap to 10.
`Iterations` counts independent review cycles entered.

### 1. Frame

Capture every input field. Establish one reviewable scope.

Proceed only when:

- Each field has evidence or `not stated`.
- Acceptance criteria distinguish a fix from a behavior change.
- No unresolved conflict blocks a safe next action.

### 2. Reproduce and Diagnose

Run the narrowest available reproduction before editing.
If direct reproduction is infeasible, trace the most specific supported failing path.
Use code, tests, logs, errors, or user evidence.

Proceed only when:

- You reproduce the failure or record one concrete failing path.
- You know the expected behavior.
- A root-cause hypothesis explains both.

Report `BLOCKED` when neither reproduction nor a supported failing path is available.

### 3. Repair

Apply the smallest change that fixes the root cause and preserves behavior outside the framed scope.
Follow Decision Mode before implementing a user-owned choice.

Proceed only when:

- You changed the root-cause path.
- Every touched line serves the fix.
- No known caller or affected behavior remains on the obsolete path.

### 4. Verify

Repeat the reproduction or strongest available check that exercises the repaired path.
Run additional targeted checks proportional to the regression risk.
Record commands or scenarios, results, unavailable checks, and residual risk.
Repeat this step after every later code change.

Proceed only when:

- Current evidence exercises the repaired path.
- Proportional regression checks ran, or each unavailable check has a recorded reason.
- You recorded all results and residual risks.

### 5. Review

Bring the change into compliance with the contract's `Scope and Quality` criteria.
Build the complete packet from its `Review Packet` section.

Use a fresh reviewer context when the environment provides one.
Otherwise, freeze the complete packet. Finish a review-only pass before returning to author work.
This fallback separates reviewer and author phases without claiming context isolation.
An ordinary author self-check does not qualify.

Increment `Iterations` once for the cycle. Run the review.
Check the result against the Reviewer Output Contract.
For a malformed result, retry once with the violated output requirement emphasized.
Keep the retry in the same cycle without incrementing `Iterations`.
Report `BLOCKED` after a second malformed result.

Proceed only when:

- The packet includes every required item, including repeated-review fields when applicable.
- You incremented `Iterations` exactly once for the cycle.
- You used the specified fresh-context or frozen-packet method.
- The result satisfies the Reviewer Output Contract and evaluates every review criterion.

### 6. Triage and Resolve

For `FAIL`, classify and handle every finding under the contract's `Finding Dispositions`.
A sustaining `FAIL` has directly actionable findings without an unresolved user decision or hard boundary.

At review 10, classify and report sustaining findings. Stop as `FAIL` before applying further changes.
This stop prevents an unreviewed fix from appearing complete.

Resolution is complete when every finding has exactly one disposition and you finish its required action or report placement.

### 7. Repeat

After resolving a sustaining `FAIL`, re-read changed targets. Repeat verification.
Run Review with the updated packet.

Proceed only when the targets and verification are current and the review result is valid.
For `PASS`, proceed to Stop and Report. For `FAIL`, return to Triage and Resolve.

## Stop and Report

Use the contract's Final Output Format.

- `PASS`: the evidence satisfies every review criterion.
- `FAIL`: a non-hard-boundary user decision remains in default mode, or review 10 has sustaining findings.
- `BLOCKED`: a hard boundary, unusable required resource, or conflicting change prevents progress.
- Also report `BLOCKED` when unavailable non-decisional factual evidence cannot support a safe fix.
- Also report `BLOCKED` when both attempts at one review produce malformed results.

Unresolved user-owned choices are `FAIL` unless they cross a hard boundary.
`BLOCKED` takes precedence.
Only `PASS` means the fix-review loop is complete.
