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

Provide every packet item inline or through an accessible path.
Treat missing evidence as review evidence. Do not assume it exists.

## Review Criteria

Return `PASS` only when the evidence satisfies every applicable criterion:

### Correctness

- The packet evidence satisfies the skill's Repair completion criterion.
- Observed and expected behavior match the user request and evidence.
- Callers, boundaries, state transitions, async behavior, concurrency, types, and error paths affected by the change remain correct.
- The packet reveals every known security, privacy, data-loss, migration, compatibility, or external-service risk.

### Verification

- The packet evidence satisfies the skill's Verification completion criterion.
- Tests preserve the original failure signal and observable contract.
- Tests are not weakened, overfitted, deleted, or changed to suppress failure.
- No known runtime, test, type, lint, or build failure remains within the reviewed scope.

### Scope and Quality

- The change satisfies the skill's Repair completion criterion without avoidable implementation scope.
- The change follows existing repository patterns rather than adding a parallel convention.
- Every new helper, abstraction, wrapper, dependency, configuration, file, and public name is necessary for the fix.
- Observed risk justifies every fallback, retry, log, comment, and defensive branch.
- No unrelated refactor, rename, formatting churn, unused artifact, dead branch, generic addition, or avoidable complexity remains.

### Decisions

- Every user-owned choice complies with the supplied Decision Mode policy.
- Delegated choices record the chosen option, evidence, and residual risk.
- No delegated choice crosses a hard boundary in the skill.

Any supported violation requires `FAIL`.

## Reviewer Output Contract

Start the response with exactly `PASS` or `FAIL`. Add no heading or preamble before that line.

For `PASS`, briefly explain how the evidence satisfies every applicable criterion.

For `FAIL`, list every supported finding:

```text
FAIL

- Finding: <specific defect>
  Severity: critical | high | medium | low
  Evidence: <file, line, command result, or packet fact>
  Why it matters: <observable risk>
  Required change: <smallest sufficient correction or user decision>
```

Keep findings precise, evidence-backed, and within the reviewed scope.
Request a broad rewrite only when the current approach cannot safely fix the defect.
Mark user-owned choices explicitly instead of inventing intent.
A `FAIL` without a supported finding is invalid.
A `FAIL` that presents an out-of-scope or unsupported item as a finding is also invalid.

## Finding Dispositions

For a permitted user-owned choice in Delegated Decision Mode, first make the choice under the skill's Decision Mode.
Record the choice.
Then classify the resulting concrete correction below.
Otherwise, classify every `FAIL` finding exactly once:

- **Directly actionable bug fix**: Corrects a root cause, regression, edge case, test failure, type failure, or verification gap within scope.
- **Directly actionable cleanup**: Removes an unnecessary or unused artifact, unrelated refactor, weakened test, generic comment, excessive logging, or avoidable abstraction introduced by the change.
- **User decision needed**: In default mode, identifies a user-owned choice defined by the skill's Decision Mode that does not cross a hard boundary.
- **Hard-boundary blocker**: Identifies an action or choice prohibited by the skill's hard boundaries.

Summarize every valid finding under `Review Result`, then apply this precedence:

1. For any hard-boundary blocker:
   - Restore only loop-authored boundary-crossing changes to their pre-loop baseline.
   - List the blocker under `Remaining Issues`.
   - Return the disposition to the workflow for `BLOCKED`.
2. Otherwise, for any user decision:
   - Restore only loop-authored changes that implement the undecided choice.
   - Apply no other findings.
   - List the decision under `User Decisions Needed`.
   - List unresolved actionable findings under `Remaining Issues`.
   - Return the dispositions to the workflow for `FAIL`.
3. Otherwise, follow the skill's iteration-cap rule for directly actionable findings:
   - Apply permitted changes. List them under `Changes Applied`.
   - Leave cap-blocked changes unmodified. List them under `Remaining Issues`.

Record choices already made in Delegated Decision Mode under `Delegated Decisions Applied`.
These choices are packet context, not finding dispositions.

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
