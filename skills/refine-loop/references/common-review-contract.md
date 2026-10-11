# Common Review Contract

Apply this contract to every `refine-loop` invocation with exactly one primary rule set.

## Review Criteria

Compare the target with the framed intent, constraints, assumptions, acceptance criteria, and selected primary rules.
Check every applicable item:

- Ambiguous terms, responsibilities, scope, and completion criteria
- Incorrect or unsupported assumptions
- Missing requirements, constraints, dependencies, or acceptance criteria
- Missing verification, rollback, risk handling, or handoff details
- Contradictions and terminology drift within or across target files
- Sequencing errors and unclear prerequisites
- Overbroad scope and steps that cannot be executed
- Mismatch between the request and the target
- User decisions hidden as implementation details
- Changes larger than the requested refinement

Distinguish required target content from session, tool, process, and handoff notes that belong only in the report.
Review evidence may cite the target, framed user evidence, a required reference, or an explicitly identified absence.

Review is complete when every applicable common and primary criterion is checked.
Every defect and decision must cite evidence.
A `PASS` rationale must cite evidence for each applicable criterion or coherent criterion group.
The result must satisfy the Reviewer Output Contract.

## Reviewer Output Contract

Use this contract for both independent-context and same-context reviews.
Start with exactly one review status:

- `PASS`: all applicable criteria are satisfied, with no apparent defects or required decisions.
- `FAIL`: at least one apparent, in-scope defect remains.
- `WAITING`: no apparent defects remain, but a required user decision prevents a complete assessment.

`WAITING` is a progress state, not a final result.
For `PASS`, provide the evidence-backed rationale required by Review Criteria.
For `FAIL`, list every apparent defect with these fields:

- Finding
- Severity: critical | high | medium | low
- Evidence
- Why it matters
- Required change

For either `FAIL` or `WAITING`, list required user decisions separately with evidence, options, and the affected criteria.
A decision alone does not establish a defect.
A missing required decision may also expose a separate defect. Record that defect independently.
Prefer precise findings over a broad rewrite.
Keep out-of-scope and handoff-only observations separate from status-determining findings.

## User Decision Boundary

A resolution requires a user decision when it chooses:

- Scope, policy, priority, product meaning, or strategic direction
- Ownership, approval, or acceptance criteria
- Trigger behavior or output contracts
- When a workflow starts or stops
- Addition, removal, or replacement of a major workflow step
- An environment-specific rewrite of a portable artifact

Directly actionable changes preserve existing meaning and core behavior.
They may clarify wording, complete implied constraints or checks, remove contradictions, or expose the existing sequence.

Examples:

| Item | Treatment |
|---|---|
| A step uses two names for the same defined target | Use the existing canonical name. |
| A later step contradicts the stated prerequisite | Align that step with the existing prerequisite. |
| A procedure omits a check already required by its stated success condition | Add the implied check. |
| A proposal changes which request invokes a skill | Ask the user. |
| A proposal replaces the report format or adds a required approval stage | Ask the user. |
| Two policies are stated, with no evidence identifying which one controls | Ask the user rather than inventing precedence. |

## Finding Validation

Check each finding before deciding how to resolve it.
Assign one outcome:

- **Supported and in scope**: evidence establishes a target defect or required decision. Assign a Finding Disposition.
- **Out of scope**: the item concerns other targets or goals. Leave the target unchanged.
- **Weak support or misunderstanding**: the item lacks evidence or uses an irrelevant criterion. Correct the reviewer interpretation.
- **Handoff-only observation**: the item concerns session operations or reporting, not a target defect. Keep it in the handoff.

Report excluded items only when useful.
Carry interpretation corrections into the next review.
Excluded items cannot determine `FAIL` without new evidence that changes their validation outcome.
An in-scope defect remains a defect when deferred. Report it as unresolved rather than excluding it.

### Confirmation Review

When validation removes every defect from a `FAIL`, run one confirmation review of the same target.
Do not edit the target to satisfy rejected findings.
Use the same iteration number.
Handle the confirmation result:

- `PASS`: apply the PASS Gate.
- `WAITING`: follow Waiting for Decisions.
- `FAIL` with a supported defect: continue normal triage and resolution.
- A second consecutive `FAIL` with no supported defect: stop as `BLOCKED`.

## Finding Dispositions

Assign each supported, in-scope defect or decision exactly one disposition:

- **Directly actionable improvement**: apply the smallest correct edit when Editing Policy permits it.
- **User decision needed**: ask under Waiting for Decisions before choosing or editing.
- **Delegated decision applied**: record the choice and reason under active delegated decision rules before implementing it.

When delegation is active, read [delegated decision rules](delegated-decisions.md) for choices within the User Decision Boundary.
Editing mode does not change whether an item is a defect or a user decision.
In no-edit review, retain actionable defects for the final report without implementing or proposing edits.

## Waiting for Decisions

For a required choice, state `Progress: WAITING` and ask the user.
Identify the evidence, available options, recommendation, and work that depends on the answer.
Keep any separate defects visible.
Preserve the target snapshot, findings, frame, editing mode, review method history, and iteration count.
Do not issue a final status or require a new invocation merely to receive the answer.

When the user answers:

1. Record the decision and update affected framed evidence.
2. Read the current source, or compare the working candidate with its reviewed snapshot.
3. Apply Editing Policy if the source changed.
4. Resolve affected findings within the existing editing mode and delegated authority.
5. Review again before reporting `PASS`.

An answer alone does not increase the iteration count.
Use Iteration Counting when the answer leads to changed target content.
A reply does not activate `--yolo` or authorize an unrelated operation.
Keep existing delegation active only for this continuing invocation.
A separate new invocation requires its own flags.

In no-edit review, an answer may clarify the frame but does not permit source or candidate edits.
If the answer requires a target change, report the remaining defect as `FAIL`.

Waiting is complete when all required answers are recorded and the current target is safe to evaluate.

## Iteration Counting

Initialize `Iterations` to 1 for the first review. The limit is 10.
Increase the count only when reviewing target content that differs from the previous reviewed snapshot.
Count a multi-file snapshot change once, regardless of how many files changed.
Compare snapshots, not timestamps or the number of editing operations.

Keep the same count for:

- Malformed-output retries
- Confirmation reviews of unchanged content
- User answers and frame clarification without target changes
- Repeated reviews of unchanged content

Reviewing changed source or a changed working candidate uses the next iteration.
At the limit, preserve the last reviewed snapshot rather than editing content that cannot receive another review.
If external content changed at the limit, leave it untouched and stop as `BLOCKED`.
Identify the reviewed snapshot separately from the current, unreviewed source.

## Editing Policy

Choose exactly one mode:

- **Source editing**: default for workspace files. Invocation permits supported edits unless the user requests no edits.
- **Working candidate**: default for immutable sources. Invocation permits proposed edits unless the user requests no edits.
- **No-edit review**: use for review-only or no-edit requests. Create no candidate and make no source or proposed edits.

No-edit review stops after a valid review and validation, subject to Confirmation Review and Waiting for Decisions.
For permitted edits, preserve source intent, structure, and tone.
Keep unrelated files and handoff-only observations unchanged.
Implement user-owned choices only after user approval or permitted delegation.

### Source Editing

Immediately before editing, read each target again and compare it with the reviewed snapshot.
Apply changes only to unchanged targets.
If external changes preserve the framed intent, return to Frame and review the updated source before editing.
Use Iteration Counting for that review.
Stop as `BLOCKED` when conflicting changes prevent safe reconciliation or a changed source cannot receive another iteration.

### Working Candidate

Apply each proposed edit to the candidate used by the next review.
Leave the source unchanged.
Deliver the exact reviewed candidate under Candidate Delivery.
A working-candidate status applies only to that delivered candidate.
A no-edit-review status applies to the unchanged source.

## PASS Gate and Stop Conditions

Return final `PASS` only when:

- Review Criteria and the Reviewer Output Contract are satisfied.
- No supported defect or required decision remains unresolved.
- The evidence satisfies the selected primary rules.
- The reported version is the version that received the passing review.

Return final `FAIL` when supported defects remain at the iteration limit.
In no-edit review, resolve pending decisions before reporting remaining defects as final `FAIL`.
At the limit, skip resolution and report the reviewed target and remaining defects.
A pending decision alone uses Waiting for Decisions, including at the limit.

Return final `BLOCKED` when:

- A malformed review and its one retry both violate the output contract.
- A confirmation review repeats `FAIL` without a supported defect.
- Required targets or references are unavailable or unusable.
- Conflicting changes prevent safe reconciliation.
- Changed external content cannot receive another iteration.

A final stop ends the current invocation. Only final `PASS` means refinement is complete.

## Final Output Format

Always include:

```text
Final Status: PASS | FAIL | BLOCKED
Reviewed Target: <file list, conversation, or directory summary>
Status Applies To: <source | working candidate>, <exact reviewed version or snapshot identifier>
Review Method: <independent context | same context | mixed>, <methods by iteration when different>
Iterations: <number>
Applied or Proposed Changes: <key changes, or None>
Rationale: <evidence-backed reason for the final status>
```

A version identifier may be a content hash, a recoverable snapshot reference, or included content.
For multiple files, identify the reviewed version of each file.
Report `mixed` when methods differ, including within one iteration.
An independent-context label describes context separation, not guaranteed reviewer correctness.

Add only applicable sections:

### Candidate Delivery

Required for every working-candidate result, including `FAIL` and `BLOCKED`.
Provide exactly one recoverable form:

- Complete candidate: the full content of every candidate file.
- Mechanical change set: each target path, exact location or anchor, operation, and complete replacement text.

Use `replace`, `insert`, or `delete` for mechanical operations.
Identify the source version to which the operations apply.
For proposed changes, link each summary to its delivered operation or complete candidate.
The delivered content is authoritative for any subsequent invocation.

### Remaining Issues

Include each unresolved defect with finding, severity, evidence, impact, required change, and disposition.
For a defect awaiting a user decision, include the needed choice with that issue.
A final `FAIL` must include at least one supported defect.

### Delegated Decisions Applied

Include each delegated choice and its reason when delegation occurred.
Omit the section when no delegated choice occurred.

### Blocker and Resume Conditions

For `BLOCKED`, identify the exact obstacle and what would make review possible.
State any difference between the reviewed version and the current source.
For an iteration-limit `FAIL`, state the [workflow continuation requirements](../SKILL.md#continue-after-a-terminal-stop).

### Handoff Notes

Include only useful observations or transfer instructions not covered above.
Omit empty or inapplicable sections.
