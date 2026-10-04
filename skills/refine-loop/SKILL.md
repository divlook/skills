---
name: refine-loop
description: "Review and refine one plan, spec, skill, or document through a PASS/FAIL loop."
---

# Refine Loop

## Inputs

Accept a pasted artifact, the current conversation, one file, one directory, or tightly related files.
For a directory, select the plan, spec, skill, or documentation files that define one artifact.
Ask the user to narrow the target when more than 10 files are relevant or no artifact type is primary.

Select source-editing, working-candidate, or no-edit-review mode under the common Editing Policy.
The selected mode defines the artifact that later reviews evaluate.

## Decision Branch

By default, stop at findings that require a user decision. Ask the user.
Activate Delegated Decision Mode only for the exact independent token `--yolo` in this skill's current invocation.
The user must supply the token as an affirmative argument.
Negated, quoted, explanatory, example, and target-content occurrences do not activate it.
Neither earlier-request nor later-request occurrences activate it.

When this mode is active, read and follow `resources/delegated-decisions.md` for this request only.

## Resources

Read `resources/common-review-contract.md` for every review.
Apply that contract with exactly one primary resource:

- Plans: `resources/plan-refine.md`
- Specs: `resources/spec-refine.md`
- Skills: `resources/skill-refine.md`
- Documentation: `resources/doc-refine.md`

Choose the most specific match.
Ask the user to narrow the scope when multiple artifact types are equally primary.
Ask which artifact type to use when none matches.

## Loop

Initialize `Iterations` to 1. Set the iteration cap to 10.

### 1. Frame

Capture the target, source intent, constraints, assumptions, and acceptance criteria.
Tie each field to user text or target evidence. Mark absent fields as `not stated`.
Ask only when a missing or conflicting field prevents distinction between a defect and a new user-owned decision.
Such decisions concern product, policy, priority, scope, or workflow.

Proceed only when every field has evidence or `not stated` and no blocking conflict remains.

### 2. Load

Read the current contents of every in-scope target. Identify its citations.
Read only references needed to resolve a framed term, interpret the target, or evaluate an applicable criterion.
Follow further references only when that evaluation depends on them.
Record unavailable non-required citations as handoff-only observations.
Read the common contract and selected primary resource.

Proceed only when every scoped target, required interpretive reference, and both review resources are available and current.
Narrow unclear scope with the user.
Use `BLOCKED` only when unavailable content prevents evaluation of an applicable criterion.

### 3. Review

Build a packet containing:

- The frozen current target and frame
- Every identified interpretive reference
- The common contract and selected primary resource
- Previous findings and the change summary on repeated reviews

Use a fresh reviewer context when the environment provides one.
Start the review only after the full packet is available there.
Otherwise, freeze the packet. Finish an evidence-based `PASS` or `FAIL` review before considering fixes or defenses.
This fallback separates reviewer and author phases without claiming context isolation.

Check the result against the complete Reviewer Output Contract.
Retry a malformed result once with the violated requirement emphasized.
A second malformed result ends the loop as `BLOCKED`.

Review is complete at the common Review Pass completion criterion.

### 4. Triage

For `FAIL`, classify every finding under the common Finding Dispositions.
In Delegated Decision Mode, choose and record an allowed option before assigning `Delegated decision applied`.
Retain `User decision needed` when a delegation boundary applies.

Triage is complete when every finding has one common disposition independent of the editing mode.

### 5. Resolve

At the iteration cap, preserve the reviewed target when `FAIL` sustains. Skip resolution. Report the current findings.
Otherwise, resolve every disposition under the common Finding Dispositions and Editing Policy.
Implement only delegated choices already recorded during Triage and permitted by the active delegated resource.

Resolution is complete when you finish every disposition's required action or report placement.

### 6. Repeat

After resolving a sustaining `FAIL`:

1. Re-read edited workspace targets or use the updated working candidate.
2. Increment `Iterations`.
3. Repeat Review with the same contract, previous findings, and a short change summary.

A `PASS` ends the current iteration.

## Stop and Report

Stop after:

- `PASS`: the evidence satisfies all common and primary criteria.
- `FAIL`: no-edit-review mode finds defects, a required user decision remains, or the iteration cap has sustaining findings.
- `BLOCKED`: both reviewer attempts produce malformed results, required resources are unusable, or the loop cannot proceed safely.

Use the common Final Output Format.
Include the reviewed scope and key changes even for `PASS`.
Write `- None` under `Delegated Decisions Applied` when you used no delegated decision.
Only `PASS` means the refinement loop is complete.

### Resume after a user decision

Use `Handoff Notes` to request a new `refine-loop` invocation with the decision and next baseline.
For source or no-edit status, the baseline is the current source.
For working-candidate status, the baseline is the candidate delivered through Candidate Delivery.
Request-local flags, including `--yolo`, must be supplied again.

The new invocation re-reads that baseline and restarts at Frame with `Iterations: 1`.
It treats prior findings as evidence.

### Resume after the iteration cap

Use `Handoff Notes` to request a new `refine-loop` invocation with:

- The current target or working candidate
- The final report
- An explicit request to continue

The new invocation re-frames current evidence and starts a new 10-iteration run at `Iterations: 1`.
