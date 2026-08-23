---
name: refine-loop
description: "Review and refine one plan, spec, skill, or document through a PASS/FAIL loop."
disable-model-invocation: true
---

# Refine Loop

## Inputs

Accept a pasted artifact, the current conversation, one file, one directory, or a tightly related file set.

For a directory, scope the loop to the plan, spec, skill, or documentation files that define one artifact. Ask the user to narrow the target when more than 10 files are relevant or no single artifact type is primary.

Select source-editing, working-candidate, or no-edit-review mode using the common Editing Policy. The selected mode defines which artifact later reviews evaluate.

## Decision Branch

By default, stop at findings that require a user decision and ask the user. Activate Delegated Decision Mode only when the exact independent token `--yolo` is supplied as an affirmative argument to this skill's invocation in the current request. Negated, quoted, explanatory, example, target-content, earlier-request, and later-request occurrences do not activate it.

When active, load and follow `resources/delegated-decisions.md` for this request only.

## Resources

Apply `resources/common-review-contract.md` in every review and exactly one primary resource:

- Plans: `resources/plan-refine.md`
- Specs: `resources/spec-refine.md`
- Skills: `resources/skill-refine.md`
- Documentation: `resources/doc-refine.md`

Choose the most specific match. Ask the user to narrow the scope when multiple artifact types are equally primary. Ask which artifact type to use when none matches.

## Loop

Initialize `Iterations` to 1 and the iteration cap to 10.

### 1. Frame

Capture the target, source intent, constraints, assumptions, and acceptance criteria. Tie each field to user text or target evidence; mark absent fields as `not stated`. Ask the user only when a missing or conflicting field prevents the review from distinguishing a defect from a new product, policy, priority, scope, or workflow decision. Framing is complete when every field has evidence or an explicit `not stated` marker and no blocking conflict remains.

### 2. Load

Read the current contents of every in-scope target and identify its citations. Load only references needed to resolve a framed term, interpret the target, or validate an applicable criterion; follow further references only when that evaluation depends on them. Record unavailable non-required citations as handoff-only observations. Load the common contract and selected primary resource. Loading is complete when every scoped target, required interpretive reference, and both review resources are available and current. Narrow unclear scope with the user; use `BLOCKED` only when unavailable content prevents an applicable criterion from being evaluated.

### 3. Review

Build a review packet containing the frozen current target, frame, every identified interpretive reference, common contract, and selected primary resource; on repeats, include previous findings and the change summary. Run the review in a fresh reviewer context when the environment provides one, starting only after the full packet is available there. Otherwise, enforce a review-first pass: freeze the packet, finish the evidence-based `PASS` or `FAIL` result, and only then consider fixes or defenses. This fallback separates the reviewer and author phases without claiming context isolation.

Validate the result against the complete Reviewer Output Contract. Retry a malformed result once with the violated requirement emphasized. A second malformed result ends the loop as `BLOCKED`.

Review is complete at the common Review Pass completion criterion.

### 4. Triage

For `FAIL`, classify every finding under the common Finding Dispositions. When Delegated Decision Mode is active, choose and record an allowed delegated option during Triage before assigning `Delegated decision applied`; retain `User decision needed` when a delegation boundary applies. Triage is complete when every finding has one common disposition independent of the editing mode.

### 5. Resolve

When the current iteration reaches the iteration cap with a sustaining `FAIL`, preserve the reviewed target, skip resolution, and report the current findings.

Otherwise, resolve every disposition according to the common Finding Dispositions and Editing Policy. Implement only delegated choices already recorded during Triage and permitted by the active delegated resource. Resolution is complete when every disposition's required action or report placement is finished.

### 6. Repeat

After resolving a sustaining `FAIL`, re-read edited workspace targets or use the updated working candidate, increment `Iterations`, and re-review with the same contract, previous findings, and a short change summary. A `PASS` ends the current iteration.

## Stop and Report

Stop after:

- `PASS`: all common and primary criteria are satisfied.
- `FAIL`: no-edit-review mode finds defects, a required user decision remains, or the iteration cap has sustaining findings.
- `BLOCKED`: an initial reviewer output and its retry for the same review are both malformed, required resources are unusable, or the loop cannot proceed safely.

Use the common Final Output Format. Include the reviewed scope and key changes even for `PASS`. Under `Delegated Decisions Applied`, write `- None` when no delegated decision was used.

When stopping for a user decision, use `Handoff Notes` to tell the user to re-invoke `refine-loop` with the decision and the next baseline: the current source for source or no-edit status, or the candidate delivered by the common Candidate Delivery field. Request-local flags, including `--yolo`, must be supplied again. The new invocation re-reads that baseline, restarts at Frame with `Iterations: 1`, and treats prior findings as evidence.

When the iteration cap stops the run, use `Handoff Notes` to tell the user to re-invoke `refine-loop` with the current target or working candidate, the final report, and an explicit request to continue. The new invocation re-frames current evidence and starts a new 10-iteration run at `Iterations: 1`.

Only `PASS` means the refinement loop is complete.
