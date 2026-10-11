# Refine Loop

Refine Loop reviews and improves one plan, specification, skill, or document through an evidence-backed PASS/FAIL loop.

## When to Use It

Use Refine Loop for:

- Execution plans and implementation strategies
- Requirements, design specifications, task specifications, and acceptance criteria
- Agent skill packages and their references
- README files, guides, policies, and general documentation

The target can be pasted content, the current conversation, one file, one directory, or related files.
Connected files belong together when they define one artifact with a shared purpose and completion criteria.
There is no file-count limit.
The skill asks for a scope choice when independent artifacts are mixed or the target is unclear.

## Invocation

Invoke the skill with the artifact or path:

```text
/refine-loop docs/migration-plan.md
```

For review without changes:

```text
/refine-loop Review this specification without editing it: <specification>
```

Add the independent `--yolo` token to delegate the smallest reasonable choice for a user-owned decision:

```text
/refine-loop --yolo docs/search-spec.md
```

The flag applies only to that invocation, including replies to its pending questions.
A reply cannot activate the flag after invocation.
A new invocation requires the flag again.
The flag does not authorize security changes, data migration, production operations, external side effects, commits, pushes, or pull requests.
It also does not authorize credentials, permissions, billing changes, or deployments.

## Editing Modes

- **Source editing:** Default for workspace files. The skill applies the smallest supported edits.
- **Working candidate:** Default for immutable sources. The skill returns complete candidate content or exact mechanical changes.
- **No-edit review:** Used when requested. The source remains unchanged, and the skill creates no candidate or proposed edits.

Clarifying a decision does not change the selected editing mode.
A working-candidate result applies only to the delivered candidate, not the original source.

## Review and User Decisions

The skill records intent and constraints, then loads the common contract and one artifact-specific rule set.
It reads only references needed to interpret or evaluate the target.
It separates supported defects from unsupported findings and observations that belong only in the handoff.

Wording corrections that preserve existing meaning can be automatic.
Changes to scope, policy, invocation, output contracts, major steps, or stop conditions remain user decisions unless delegated.

When a decision is needed, the skill reports `Progress: WAITING` and asks a question.
Reply in the same conversation. A new skill invocation is not required.
The reply resumes the current run with its findings, editing mode, and iteration count preserved.
A decision alone is not a defect. Separate defects remain visible while the skill waits.
The skill checks the current source before continuing and reviews again before reporting `PASS`.

For example, choosing between two valid report formats requires a decision.
Correcting two names for the same defined object preserves meaning and does not require a new policy choice.

## Review Method and Iterations

The skill uses a fresh reviewer context when available.
Otherwise, it separates review and editing in the same context.
The final report identifies `independent context`, `same context`, or `mixed` review methods.
Context separation does not guarantee reviewer correctness.

The first review counts as iteration 1. The limit is 10.
The count increases only when changed target content receives a review.
Changing multiple connected files before one review counts as one new iteration.
Retries, confirmation reviews, and answers without target changes keep the same count.

At iteration 10, remaining defects produce `FAIL` without further edits.
A decision-only question can still wait for an answer.
External changes that require an eleventh review produce `BLOCKED`, leaving the current source untouched.
Continuing after a final iteration-limit stop requires a new invocation with the current target, final report, and explicit continuation request.

## Results

Final results are:

- **PASS:** The reported version satisfies all applicable criteria, with no unresolved defects or required decisions.
- **FAIL:** Supported defects remain in no-edit review or at the iteration limit.
- **BLOCKED:** Required content is unavailable, review output remains invalid, or conflicting changes prevent safe continuation.

`WAITING` is progress, not a final result. Only final `PASS` means refinement is complete.

Every final report includes status, target and reviewed version, review method, iteration count, key changes, and supporting evidence.
The report includes other sections only when applicable:

- Recoverable candidate content or exact mechanical changes for working-candidate results
- Remaining defects, with evidence, impact, required changes, and disposition
- Delegated choices and reasons
- Exact obstacles and resume conditions for blocked or iteration-limit results
- Useful handoff notes

Empty and inapplicable sections are omitted.
