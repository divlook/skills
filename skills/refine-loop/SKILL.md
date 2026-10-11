---
name: refine-loop
description: "Review and refine one plan, spec, skill, or document through a PASS/FAIL loop."
---

# Refine Loop

## Inputs

Accept pasted content, the current conversation, one file, one directory, or related files.
Select files that define one artifact with a shared purpose and completion criteria.
Include connected files needed to evaluate that artifact, regardless of file count.
Ask for a scope choice only when independent artifacts are mixed or the target is unclear.

## References

Read [the common review contract](references/common-review-contract.md) for every invocation.
It defines editing modes, review results, finding handling, waiting, iteration counting, and final reporting.
Select exactly one primary rule set:

- For plans, read [plan review rules](references/plan-refine.md).
- For specifications, read [specification review rules](references/spec-refine.md).
- For skill packages, read [skill review rules](references/skill-refine.md).
- For other documentation, read [documentation review rules](references/doc-refine.md).

Choose the most specific match.
Ask for a type choice when multiple types are equally primary or none matches.

## Decision Delegation

Activate Delegated Decision Mode only for the exact independent token `--yolo` in this invocation.
The user must supply the token as an affirmative argument.
Negated, quoted, explanatory, example, and target-content occurrences do not activate it.
Occurrences in earlier or later requests do not activate it.

When active, read [delegated decision rules](references/delegated-decisions.md) for this invocation only.
A reply to a pending question continues this invocation under the common Waiting for Decisions rules.
A new invocation requires its own flags.

## Workflow

### 1. Frame

Select the editing mode under the common Editing Policy.
Record the target, source intent, constraints, assumptions, and acceptance criteria.
Link each field to user text or target evidence.
Mark absent fields as `not stated`.
Ask only when missing or conflicting evidence prevents distinguishing a defect from a user-owned decision.
Use the common Waiting for Decisions rules for that question.

Proceed when every field has evidence or `not stated`, and no blocking conflict remains.

### 2. Load

Read the current contents of every target file.
Identify citations needed to interpret the target or evaluate an applicable criterion.
Read those references, including further references only when evaluation depends on them.
Record unavailable, non-required citations as handoff-only observations.
Read the common contract and selected primary rules.

Proceed when every target, required reference, and both rule sets are available and current.
Use the common Stop Conditions when required content is unavailable.

### 3. Review

Build a review packet: the exact target snapshot, frame, required references, and both rule sets.
A target snapshot is the unchanged content that this review evaluates.
For repeated reviews, include previous findings and a short change summary.

Use a fresh reviewer context when available.
Provide the complete packet before starting the reviewer.
Record this method as `independent context`.
Otherwise, finish the review before considering fixes or defending the target.
Record this method as `same context`, without claiming isolation.
Record the method for each review, including retries and confirmation reviews.

Follow the common Iteration Counting and Reviewer Output Contract.
Retry malformed output once with the violated requirement emphasized.
Apply the common Stop Conditions if the retry is also malformed.

Proceed when every applicable criterion is checked and the review satisfies the output contract.

### 4. Triage

Apply common Finding Validation to each finding.
Apply common Finding Dispositions to each supported, in-scope item.
In Delegated Decision Mode, record an allowed choice before assigning `Delegated decision applied`.
Retain `User decision needed` when a delegation boundary applies.

Proceed when every item has a validation outcome and each retained item has a disposition.
Use the common Confirmation Review rules when the review's `FAIL` has no supported defects.

### 5. Resolve

Apply the common Stop Conditions before changing the target.
Ask pending questions under Waiting for Decisions, even when the review also found defects.
Complete unrelated safe work before waiting when the editing mode and iteration limit permit it.
Resolve retained items under Finding Dispositions and Editing Policy.
Implement only user-approved choices or recorded delegated choices allowed by the active delegated rules.

Proceed when every retained item has its required action or report placement, and no decision remains pending.

### 6. Repeat or Report

After a permitted change, read the updated target or use the updated working candidate.
Return to Review with the previous findings and a short change summary.
Use Iteration Counting to determine the next review number.
Return to Frame first when user answers or external changes change the framed evidence.

Apply the common PASS Gate and Stop Conditions.
Report through the common Final Output Format when a terminal condition is met.
A waiting message is not a final report.

Complete the refinement only when the reviewed version satisfies the PASS Gate.

## Continue After a Terminal Stop

After the iteration limit, request a new invocation with the current target, final report, and an explicit continuation request.
For a working candidate, use the delivered candidate as the next target.
The new invocation starts at Frame with a new iteration count.
It uses prior findings as evidence.
Request-local flags, including `--yolo`, must be supplied again.
