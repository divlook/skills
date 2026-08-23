# Fix Review Contract

Apply this contract to every independent review in `fix-review-loop`.

## Review Packet

The complete review packet contains:

- This full review contract
- The full active `fix-review-loop` skill
- The user request and framed observed and expected behavior
- The reviewed scope and constraints
- Pre-fix reproduction or failing-path evidence
- The actual current code change
- Verification commands or scenarios, their results, unavailable checks, and residual risks
- The selected Decision Mode and every delegated decision
- Previous findings and the change summary on repeated reviews

Every item must be present inline or through an accessible path. Missing evidence is itself review evidence; the reviewer must not assume it exists.

## Review Criteria

Return `PASS` only when every applicable criterion is satisfied:

### Correctness

- The skill's Repair completion criterion is satisfied by the packet evidence.
- Observed and expected behavior match the user request and evidence.
- Callers, boundaries, state transitions, async behavior, concurrency, types, and error paths affected by the change remain correct.
- No known security, privacy, data-loss, migration, compatibility, or external-service risk is concealed.

### Verification

- The skill's Verification completion criterion is satisfied by the packet evidence.
- Tests preserve the original failure signal and observable contract; none are weakened, overfitted, deleted, or changed to suppress failure.
- No known runtime, test, type, lint, or build failure remains within the reviewed scope.

### Scope and Quality

- The skill's Repair completion criterion is satisfied without avoidable implementation scope.
- The change follows existing repository patterns rather than adding a parallel convention.
- Every new helper, abstraction, wrapper, dependency, configuration, file, and public name is necessary for the fix.
- Every fallback, retry, log, comment, and defensive branch is justified by observed risk.
- No unrelated refactor, rename, formatting churn, unused artifact, dead branch, generic addition, or avoidable complexity remains.

### Decisions

- Every user-owned choice complies with the supplied Decision Mode policy.
- Delegated choices record the chosen option, evidence, and residual risk.
- No delegated choice crosses a hard boundary in the skill.

Any supported violation requires `FAIL`.

## Reviewer Output Contract

The first line of the response must be exactly `PASS` or `FAIL`; add no heading or preamble before it.

For `PASS`, briefly state why every applicable criterion is satisfied.

For `FAIL`, list every supported finding:

```text
FAIL

- Finding: <specific defect>
  Severity: critical | high | medium | low
  Evidence: <file, line, command result, or packet fact>
  Why it matters: <observable risk>
  Required change: <smallest sufficient correction or user decision>
```

Findings must be precise, evidence-backed, and within the reviewed scope. Request a broad rewrite only when the current approach cannot safely fix the defect. Mark user-owned choices explicitly instead of inventing intent.
A `FAIL` with no supported finding, or with an out-of-scope or unsupported item presented as a finding, is invalid under this contract.

## Finding Dispositions

When Delegated Decision Mode is active and a finding surfaces a permitted user-owned choice, first make and record the choice under the skill's Decision Mode, then classify the resulting concrete correction below. Otherwise, classify every `FAIL` finding exactly once:

- **Directly actionable bug fix**: Corrects a root cause, regression, edge case, test failure, type failure, or verification gap within scope.
- **Directly actionable cleanup**: Removes an unnecessary or unused artifact, unrelated refactor, weakened test, generic comment, excessive logging, or avoidable abstraction introduced by the change.
- **User decision needed**: In default mode, identifies a user-owned choice defined by the skill's Decision Mode that does not cross a hard boundary.
- **Hard-boundary blocker**: Identifies an action or choice prohibited by the skill's hard boundaries.

Summarize every valid finding under `Review Result`, then apply this precedence:

1. For any hard-boundary blocker, restore only loop-authored boundary-crossing changes to their pre-loop baseline, list the blocker under `Remaining Issues`, and return the disposition to the workflow for `BLOCKED`.
2. Otherwise, for any user decision, restore only loop-authored changes that implement the undecided choice, apply no other findings, list the decision under `User Decisions Needed` and unresolved actionable findings under `Remaining Issues`, and return the dispositions to the workflow for `FAIL`.
3. Otherwise, handle directly actionable findings under the skill's iteration-cap rule: apply and list permitted changes under `Changes Applied`, or leave cap-blocked changes unmodified and list them under `Remaining Issues`.

Record choices already made under active Delegated Decision Mode under `Delegated Decisions Applied`; they are packet context, not finding dispositions.

## Final Output Format

```text
Final Status: PASS | FAIL | BLOCKED
Iterations: <independent review cycles entered>
Reviewed Target: <file list or scope summary>

Changes Applied:
- ...

Verification:
- <command or scenario and result>

Review Result:
- ...

Remaining Issues:
- ...

User Decisions Needed:
- ...

Delegated Decisions Applied:
- <decision, evidence, residual risk>
```

Use `- None` for an empty section. Even for `PASS`, report the reviewed scope, key changes, verification, and independent review result.
