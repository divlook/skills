# Common Review Contract

Apply this contract to every `refine-loop` review together with exactly one primary case resource.

## Review Pass

Compare the artifact with the framed intent, constraints, assumptions, acceptance criteria, and selected case rules. Check:

- Ambiguous terms, responsibilities, scope, and completion criteria
- Incorrect or unsupported assumptions
- Missing requirements, constraints, dependencies, or acceptance criteria
- Missing verification, rollback, risk handling, or handoff details
- Contradictions and terminology drift within or across target files
- Sequencing errors and unclear prerequisites
- Overbroad scope and non-executable steps
- Mismatch between the request and the artifact
- User decisions hidden as implementation details
- Changes larger than the requested refinement

When content appears missing, distinguish what the artifact must contain from session, tool, process, and handoff notes that belong only in the final report.

Review evidence may cite the target, framed user or conversation evidence, an interpretive reference in the review packet, or an explicitly identified absence.

A review pass is complete when every applicable common and primary criterion has been checked, the `PASS` rationale or every `FAIL` finding cites review evidence, and the Reviewer Output Contract is satisfied.

## Reviewer Output Contract

Use this structure when running or delegating the independent review. Adapt the surrounding wording to the available mechanism while preserving the output contract.

```text
Review the provided artifact or document set as an independent reviewer.

Apply the common review contract and the selected primary refinement rules.
Check every applicable criterion against review evidence and the framed intent.

Start with exactly one top-level status: PASS or FAIL.

PASS requires a brief rationale citing review evidence for each applicable criterion or coherent criterion group.

FAIL requires at least one apparent, in-scope artifact defect or required user
decision. List every finding with all fields:
- Finding
- Severity: critical | high | medium | low
- Evidence
- Why it matters
- Required change

Prefer precise findings over a broad rewrite.
Mark choices governed by the User Decision Boundary as user decisions.
Keep out-of-scope and handoff-only observations from determining the status.
```

## User Decision Boundary

A finding requires a user decision when resolving it would decide:

- Scope, policy, priority, product meaning, or strategic direction
- Ownership, approval, or acceptance criteria
- Trigger behavior or output contracts
- When a workflow starts or stops
- Addition, removal, or replacement of a major workflow step
- An environment-specific rewrite of a portable artifact

## Finding Dispositions

Assign every `FAIL` finding exactly one disposition:

- **Directly actionable improvement** — Clarifies or completes existing intent without changing core behavior. Apply the smallest correct edit when editing is allowed.
- **User decision needed** — Falls within the User Decision Boundary. Ask before editing unless the invoking request activated Delegated Decision Mode.
- **Delegated decision applied** — Records a User Decision Boundary choice made under Delegated Decision Mode according to `delegated-decisions.md`.
- **Out of scope or deferred** — Concerns files, implementation, workflows, or goals outside the target. Leave the artifact unchanged and report it only when useful.
- **Weak support or misunderstanding** — Lacks target evidence or applies an irrelevant criterion. Do not edit; carry the correction into the next review.
- **Handoff-only observation** — Belongs in the final handoff rather than the artifact, such as session-specific operations, tool notes, or loop observations.

Delegated decisions remain subject to `delegated-decisions.md`.

After triage, only an unresolved in-scope artifact defect or required user decision can sustain `FAIL`. When none remains, run one confirmation review in the same iteration without a resolution step. Its `PASS` ends the loop, a sustaining `FAIL` follows normal triage and resolution, and a second consecutive non-sustaining `FAIL` ends as `BLOCKED`. Out-of-scope, weakly supported, and handoff-only items must not recur as `FAIL` findings unless new review evidence changes their disposition.

## Directly Actionable Changes

Directly actionable changes include:

- Clarifying wording while preserving meaning
- Adding constraints, verification, failure handling, or open questions already implied by the artifact
- Removing contradictions or overbroad wording that conflicts with the stated goal
- Reorganizing only enough to expose the existing workflow

## Editing Policy

Choose exactly one editing mode:

- **Source editing** — Default for workspace files. Invoking `refine-loop` permits edits unless the user requests review-only or no-edit output.
- **Working candidate** — Default for immutable sources. Invoking `refine-loop` permits proposed changes unless the user requests review-only or no-edit output.
- **No-edit review** — Use when the user requests review-only or no-edit output; create no candidate and make no source or proposed edits. Stop after the first valid review and triage, subject to the shared confirmation rule under Finding Dispositions.

For editable targets:

Immediately before editing, re-read each editable target and compare it with the frozen review target. Apply changes only to an unchanged target. When new changes preserve the reviewed intent, increment `Iterations` and restart at Frame, reselect the primary resource, rerun Load, and then Review the refreshed target. If the refresh would exceed the iteration cap, or the changes conflict and cannot be reconciled safely, end as `BLOCKED`.

- Apply the smallest correct change for each actionable finding.
- Preserve source intent, structure, and tone.
- Keep user decisions as questions unless they were explicitly delegated.
- Keep unrelated files and handoff-only observations unchanged.

For working-candidate targets, apply each proposed change to the candidate used by the next review while leaving the source unchanged. Deliver the recoverable candidate through the Final Output Format's Candidate Delivery field.

A working-candidate `PASS` applies only to the delivered candidate. A no-edit-review status applies to the unchanged source.

## PASS Gate

`PASS` is valid only when the Review Pass completion criterion is met, no finding sustains `FAIL` after triage, and the selected primary resource's `PASS` criterion is satisfied.

## Final Output Format

```text
Final Status: PASS | FAIL | BLOCKED
Iterations: <number>
Reviewed Target: <conversation | file list | directory summary>
Status Applies To: <source | working candidate>
Candidate Delivery:
- For source or no-edit status: Not applicable
- For working candidate status, provide exactly one:
  - Complete candidate: <full content>
  - Mechanical change set, repeat:
    - Target path: <path or pasted artifact>
    - Exact location or anchor: <anchor>
    - Operation: <replace | insert | delete>
    - Replacement text: <complete text>

The complete candidate or mechanical change set is authoritative and becomes the next baseline.

Applied or Proposed Changes:
- For source status: <applied change summary>
- For no-edit status: None
- For working candidate status, repeat:
  - Candidate Delivery operation: <complete candidate | operation number>
  - Summary: <change summary>
  - Reason: <reason>

Remaining Issues, repeat:
- Finding: <finding>
- Severity: <critical | high | medium | low>
- Evidence: <review evidence>
- Why it matters: <impact>
- Required change: <change>
- Disposition: <common disposition>

User Decisions Needed:
- ...

Delegated Decisions Applied:
- ...

Handoff Notes:
- ...
```
