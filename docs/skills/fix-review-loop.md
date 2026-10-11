# Fix Review Loop

Fix Review Loop takes one code defect through reproduction, root-cause repair, targeted verification, and an independent PASS/FAIL review.

## When to use it

Use Fix Review Loop for:

- one bug report or pasted error
- one failing command, test, runtime path, or input
- one code target or tightly related file set
- a defect that should be fixed and independently reviewed in the same workflow

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
5. exercises the repaired path and evidence-backed regression risks without requiring permanent test additions
6. resolves concrete, supported review findings within scope, up to 10 review cycles

The loop stops at a valid `PASS`. It does not seek optional improvements in another cycle.
Another path enters scope only when the fix requires it or causes a regression there.
Unrelated pre-existing defects and cleanup remain outside the loop, including with `--yolo`.
Equivalent logic is reused when its behavior and constraints fit. Similar names alone do not justify merging implementations.
Speculative inputs, future extensibility, and stylistic preferences cannot justify a review failure.

For example, an empty-array crash requires the failing branch and directly affected callers to work correctly.
It does not authorize a generic response framework or consolidation of unrelated modules.

The final report states `PASS`, `FAIL`, or `BLOCKED` and identifies the reviewed target.
It includes changes, verification evidence, review results, remaining issues, and decisions.
Only `PASS` means the fix loop completed.
