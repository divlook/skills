# Fix Review Loop

Fix Review Loop takes one code defect through reproduction, root-cause repair, targeted verification, and a PASS/FAIL review.
It reports whether the review used a fresh context or the author’s context.

## When to use it

Use Fix Review Loop for:

- one bug report or pasted error
- one failing command, test, runtime path, or input
- one code target or tightly related file set
- a defect that should be fixed and reviewed in the same workflow

It does not apply to review-only requests or non-code artifacts.

## How to invoke it

Invoke the skill directly with the defect and available evidence:

```text
/fix-review-loop <bug report, failure, or code target>
```

Example:

```text
/fix-review-loop Search crashes when the API returns an empty items array. Reproduce with pnpm test search-empty.
```

Add the independent `--yolo` token to permit the smallest reasonable user-owned behavior decision.
The repository and request must provide enough evidence for that decision.

```text
/fix-review-loop --yolo Fix the empty search response crash.
```

The flag never authorizes secrets, permissions, security policy, persistent-data changes, production operations, external side effects, commits, pushes, pull requests, or deployments.

## What it does

Fix Review Loop:

1. fixes the scope and acceptance criteria around the reported defect and directly affected paths
2. reproduces the failure or records one evidence-backed failing path
3. checks existing implementations for reuse and applies the smallest root-cause fix
4. synchronizes affected callers, tests, documentation, and configuration, and removes elements made unnecessary by the change
5. executes the repaired path and checks evidence-backed regression risks without requiring permanent test additions
6. validates findings individually and resolves supported findings within scope, up to 10 review cycles

The loop stops at a valid `PASS`. It does not seek optional improvements in another cycle.
Another path enters scope only when the fix requires it or causes a regression there.
Unrelated pre-existing defects and cleanup remain outside the loop, including with `--yolo`.
The loop reuses equivalent logic when its behavior and constraints fit. Similar names alone do not justify merging implementations.
Speculative inputs, future extensibility, and stylistic preferences cannot justify a review failure.

For example, an empty-array crash requires the failing branch and directly affected callers to work correctly.
It does not authorize a generic response framework or consolidation of unrelated modules.

## Review methods

A fresh reviewer context provides `independent review`.
When no fresh context is available, the author freezes the complete packet and performs a separate review-only pass.
The report labels this method `self-review`, not independent review.
Both methods count toward the 10-cycle limit and use the same criteria.
Either method can produce a final `PASS` when execution evidence and the review satisfy every applicable criterion.
An ordinary author check is not a structured self-review.

The loop retains supported findings even when the same review includes unsupported or out-of-scope findings.
It reports the reason for each exclusion.
Incomplete or uninterpretable reviews require one retry within the same cycle.
Excluding every finding does not automatically turn a reviewer’s `FAIL` into `PASS`.

## Decisions and verification

In default mode, the loop pauses changes that depend on a user-owned decision.
It continues independent repairs and checks only when evidence establishes correctness without that decision.
Inseparable changes pause together.
Completed independent changes must be verified and reviewed before the loop reports decision waiting.
At review 10, it leaves further corrections unapplied and reports them alongside the decision.

Code and log tracing can establish a failing path for diagnosis.
They cannot replace execution verification after a repair.
A temporary execution scenario can verify the repaired path when the original environment is unavailable.
When no execution scenario is possible, the loop finishes safe repairs and available static checks.
It then reports `BLOCKED` with the exact missing environment or input.

## Final report

| Status | Meaning |
| --- | --- |
| `PASS` | Execution evidence and a valid review satisfy every applicable criterion. |
| `FAIL` | Directly actionable findings remain at review 10. |
| `NEEDS_DECISION` | Independent work is complete or stopped at the review limit, but a user-owned decision remains. |
| `BLOCKED` | A hard boundary, conflicting change, missing prerequisite, or unusable review prevents progress. |

When stopping conditions overlap, the order is `BLOCKED`, `NEEDS_DECISION`, `FAIL`, then `PASS`.
The report retains every unresolved reason, regardless of the selected status.
It identifies the scope, review cycles, actual review methods, changes, execution evidence, and review results.
It includes remaining issues and decisions when present, and records why it excluded findings.
Only `PASS` means the fix loop completed.
