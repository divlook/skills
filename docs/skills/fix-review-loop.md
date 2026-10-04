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

1. frames the observed failure, expected behavior, scope, constraints, and acceptance criteria
2. reproduces the failure or records one evidence-backed failing path
3. applies the smallest root-cause fix
4. exercises the repaired path and proportional regression checks
5. runs an independent review against correctness, verification, scope, quality, and decision rules
6. resolves actionable findings and repeats, up to 10 review cycles

The final report states `PASS`, `FAIL`, or `BLOCKED` and identifies the reviewed target.
It includes changes, verification evidence, review results, remaining issues, and decisions.
Only `PASS` means the fix loop completed.
