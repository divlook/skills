---
name: fix-review-loop
description: "Fix one code defect through reproduction, repair, execution verification, and PASS/FAIL review. Report independent review or self-review explicitly."
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

By default, pause changes that depend on such a decision. Ask the user.
Continue independent repairs and checks only when evidence establishes their correctness without that decision.
Pause inseparable changes together.

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
`Iterations` counts review cycles entered, including self-review cycles.

### 1. Frame

Capture every input field. Establish one reviewable scope.
Limit the scope to the reported defect and directly affected callers, tests, documentation, and configuration.
Keep this scope and its acceptance criteria fixed across review cycles.
Include another path only when evidence shows the fix requires it or caused a regression there.
Apply Decision Mode before changing expected behavior. The `--yolo` token does not authorize unrelated improvements.

Proceed only when:

- Each field has evidence or `not stated`.
- Acceptance criteria distinguish a fix from a behavior change.
- No unresolved conflict blocks a safe next action.

### 2. Reproduce and Diagnose

Use the user's reported failure as evidence without rerunning it merely to confirm the report.
Otherwise, run the narrowest available reproduction before editing.
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
Measure the minimum by required behavior, not changed line count.
Inspect directly connected modules and functions for an existing implementation before adding equivalent logic.
Reuse it when its behavior and constraints fit the fix. Explain any necessary separate implementation.
Check that affected callers, tests, documentation, and configuration agree with the repaired behavior.
Remove unnecessary elements introduced by this change and existing elements made obsolete by the fix.
Leave unrelated pre-existing duplication and cleanup outside the loop.

Proceed only when:

- Completed repairs address the root-cause path. Only decision-dependent repairs may remain paused.
- Every touched line serves the fix.
- No caller or affected behavior of a completed repair remains on its obsolete path.
- You accounted for existing implementation reuse and consistency of directly affected contracts.
- No change-created or fix-obsoleted unnecessary element remains.

### 4. Verify

Run the reproduction or a temporary execution scenario that exercises the repaired path.
Code and log tracing can support diagnosis, but cannot replace execution verification.
If execution is unavailable, finish safe repairs and available static checks.
Then report `BLOCKED` with the exact missing environment or input.
Run additional targeted checks proportional to the regression risk.
Tie each additional check to a directly affected behavior or an evidence-backed risk.
Permanent test additions are not a completion criterion.
Use a temporary scenario when sufficient.
Retain a regression test when it protects an observable failure or uncertain boundary.
Record commands or scenarios, results, unavailable checks, and residual risk.
Repeat this step after every later code change.

Proceed only when:

- Current execution evidence demonstrates the expected behavior on the repaired path.
- Proportional regression checks ran, or each unavailable check has a recorded reason.
- You recorded all results and residual risks.

### 5. Review

Bring the change into compliance with the contract's `Scope and Quality` criteria.
Build the complete packet from its `Review Packet` section.

Use a fresh reviewer context when available. Record this method as `independent review`.
Otherwise, freeze the complete packet and perform a review-only pass in the author's context.
Record this method as `self-review`, not independent review.
Finish the review-only pass before resuming author work.
An ordinary author check does not qualify as this structured self-review.

Increment `Iterations` once for the cycle. Run the review.
Validate each finding under the contract's `Finding Validation` section.
Keep supported findings even when the result also contains excluded findings.
Check the verdict, required fields, and criterion coverage against the Reviewer Output Contract.
For an uninterpretable or incomplete result, retry once with the violated requirement emphasized.
Keep the retry in the same cycle without incrementing `Iterations`.
Report `BLOCKED` if the retry still fails that requirement.

Proceed only when:

- The packet includes every required item, including repeated-review fields when applicable.
- You incremented `Iterations` exactly once for the cycle.
- You recorded the actual review method and used its required procedure.
- The result satisfies the Reviewer Output Contract and evaluates every review criterion.

### 6. Resolve and Repeat

Record every finding's validation result, including the reason for each exclusion.
Classify supported findings under the contract's `Finding Dispositions`.
Apply its required actions and report placement.

At review 10, classify remaining findings without applying further changes.
This limit prevents an unreviewed repair from appearing complete.
Before that limit, resolve directly actionable findings that do not depend on an unresolved user decision.
After every change, re-read changed targets and repeat Verify.
Return to Review with the updated packet.

When only decision-dependent work remains, report `NEEDS_DECISION` unless a blocker requires `BLOCKED`.
When you exclude every finding, check criterion coverage rather than automatically converting `FAIL` to `PASS`.
Use Review's retry rule if the result does not support a verdict covering every applicable criterion.
Stop at a valid `PASS`. Do not start another cycle for optional improvements.

Resolution is complete only when:

- Every finding has a validation result and every supported finding has exactly one disposition.
- You verified and reviewed every permitted action, or a stopping condition prevents further work.
- The report distinguishes remaining decisions, actionable findings, blockers, and excluded findings.

## Stop and Report

Use the contract's Final Output Format.

- `PASS`: execution evidence and a valid review satisfy every applicable criterion.
- `FAIL`: directly actionable findings remain at review 10.
- `NEEDS_DECISION`: a user-owned decision remains after Resolve and Repeat completes permitted independent work or reaches the review limit.
- `BLOCKED`: a hard boundary, unusable required resource, or conflicting change prevents progress.
- Also report `BLOCKED` when unavailable non-decisional factual evidence cannot support a safe fix.
- Also report `BLOCKED` when execution verification is unavailable.
- Also report `BLOCKED` when both attempts at one review are uninterpretable or incomplete.

Use this precedence: `BLOCKED`, `NEEDS_DECISION`, `FAIL`, then `PASS`.
Report every remaining reason even when another status takes precedence.
Report the actual review method separately from the final status.
Use `not performed` when the loop stops before Review.
Only `PASS` means the fix-review loop is complete.
