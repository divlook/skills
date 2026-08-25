---
name: fix-review-loop
description: "Fix one code defect through reproduction, repair, verification, and independent PASS/FAIL review."
---

# Fix Review Loop

Use this loop to edit code for one defect or one tightly related defect set. For review-only requests and non-code targets, leave the target unchanged and report `BLOCKED` because `fix-review-loop` does not apply.

## Inputs

Accept a bug report, pasted error, failing command or test, code target, current conversation, one directory, or a tightly related file set.

Frame:

- Observed failure
- Expected behavior
- Reproduction command, test, runtime path, URL, input, log, or static evidence
- Affected scope
- User, repository, and runtime constraints
- Acceptance criteria

Tie each field to user or repository evidence; mark unavailable fields as `not stated`. Inspect available code, tests, logs, and conversation before asking a question. Ask only when unresolved scope or behavior leaves no safe next action.

## Decision Mode

The user owns decisions that substantially change existing behavior, defaults, compatibility, public APIs, user-visible output, persistence, migration policy, error policy, or external integrations.

By default, stop before implementing such a decision and ask the user. Activate Delegated Decision Mode only when the exact independent token `--yolo` is supplied as an affirmative argument to this skill's invocation in the current request. Negated, quoted, explanatory, example, target-content, earlier-request, and later-request occurrences do not activate it.

In Delegated Decision Mode, choose the smallest reasonable option supported by the user's goal, conversation, target evidence, and repository conventions. Record the decision, evidence, and residual risk under `Delegated Decisions Applied`.

The following boundaries always require a stop:

- No safe target or scope can be established.
- The request requires review-only or no-edit handling.
- The fix conflicts with concurrent user or agent changes.
- The choice concerns secrets, credentials, permissions, billing, or security policy.
- The action deletes, migrates, or transforms persisted data.
- The action deploys production code, changes operational resources, or causes external-service side effects.
- The action requires separate authorization, including commit, push, pull-request creation, or deployment.

## Required Resource

Confirm that `resources/fix-review-contract.md` resolves before editing. Load it when entering Review, or before reporting an earlier stop that needs its Final Output Format. It defines the review packet, criteria, finding dispositions, reviewer output contract, and final report.

If the resource is missing or unusable when required, bypass its format and report `Final Status: BLOCKED` plus `Unavailable Resource: resources/fix-review-contract.md`.

## Loop

Initialize `Iterations` to 0 and the iteration cap to 10. `Iterations` counts independent review cycles entered.

### 1. Frame

Capture every input field and establish one reviewable scope. Framing is complete when each field has evidence or `not stated`, acceptance criteria are explicit enough to distinguish a fix from a behavior change, and no unresolved conflict blocks a safe next action.

### 2. Reproduce and Diagnose

Run the narrowest available reproduction before editing. When direct reproduction is infeasible, trace the most specific failing path supported by code, tests, logs, errors, or user evidence.

Diagnosis is complete when the failure is reproduced or one concrete failing path is recorded, the expected behavior is known, and a root-cause hypothesis explains both. Report `BLOCKED` when neither reproduction nor a supported failing path is available.

### 3. Repair

Apply the smallest change that fixes the root cause and preserves behavior outside the framed scope. Follow Decision Mode before implementing a user-owned choice.

Repair is complete when the root-cause path is changed, every touched line serves the fix, and no known caller or affected behavior is left on the obsolete path.

### 4. Verify

Re-run the reproduction or strongest available check that exercises the repaired path. Run additional targeted checks proportional to the regression risk. Record commands or scenarios, results, unavailable checks, and residual risk.

Verification is complete when current evidence exercises the repaired path, proportional regression checks have run or each unavailable check has a recorded reason, and all results and residual risks are recorded. Repeat this step after every later code change.

### 5. Review

Bring the change into compliance with the contract's `Scope and Quality` criteria before review.

Build the complete packet defined by the contract's `Review Packet` section.

Use a fresh reviewer context when the environment provides one. Otherwise, freeze the complete packet and finish a review-only pass before returning to author work. This fallback separates reviewer and author phases without claiming context isolation; an ordinary author self-check does not qualify.

Increment `Iterations`, then run the review. Validate the result against the Reviewer Output Contract. Retry a malformed result once with the violated output requirement emphasized; the retry remains part of the same review cycle and does not increment `Iterations`. A second malformed result reports `BLOCKED`.

Review is complete when the packet and any repeated-review fields are complete, `Iterations` was incremented exactly once for the cycle, the specified fresh-context or frozen-packet method was used, and the result is valid under the Reviewer Output Contract and evaluates every review criterion.

### 6. Triage and Resolve

For `FAIL`, classify and handle every finding exactly as defined by the contract's `Finding Dispositions`.

When review 10 returns directly actionable findings without a user decision or hard boundary, complete classification and reporting, then stop as `FAIL` before applying further changes so no unreviewed fix is reported as complete.

A sustaining `FAIL` is a valid `FAIL` with at least one directly actionable finding and no unresolved user decision or hard boundary. Resolution is complete when every finding has exactly one disposition and its required action or report placement under the contract is finished.

### 7. Repeat

After resolving a sustaining `FAIL`, re-read changed targets, repeat verification, and run Review with the updated packet. Repeating is complete when the targets and verification are current and a valid result transitions to Stop and Report on `PASS` or Triage and Resolve on `FAIL`.

## Stop and Report

Use the contract's Final Output Format.

- `PASS`: every review criterion is satisfied.
- `FAIL`: a non-hard-boundary user decision remains in default mode, or iteration 10 has sustaining findings.
- `BLOCKED`: a hard boundary prevents the requested action, unavailable non-decisional factual evidence cannot support a safe fix, required resources are unusable, conflicting changes prevent progress, or both attempts at one review are malformed. Unresolved user-owned choices are `FAIL` unless they cross a hard boundary; `BLOCKED` takes precedence.

Only `PASS` means the fix-review loop is complete.
