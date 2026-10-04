# Refine Loop

Refine Loop reviews and improves one plan, specification, skill, or document through an evidence-backed PASS/FAIL loop.

## When to use it

Use Refine Loop for:

- execution plans and implementation strategies
- requirements, design specs, task specs, and acceptance criteria
- agent skill packages and their resources
- README files, guides, policies, and general documentation

The target can be pasted content, the current conversation, one file, one directory, or a tightly related file set.
A directory must resolve to one primary artifact type and no more than 10 relevant files.

## How to invoke it

Invoke the skill directly with the artifact or path:

```text
/refine-loop <artifact or path>
```

Examples:

```text
/refine-loop docs/migration-plan.md
```

```text
/refine-loop Review this specification without editing it: <specification>
```

Add the independent `--yolo` token to permit the smallest reasonable option for a finding that requires a user-owned decision.

```text
/refine-loop --yolo docs/search-spec.md
```

The flag does not authorize secrets, permissions, security policy, data migration, production operations, external side effects, commits, pushes, pull requests, or deployments.

## Editing modes

- **Source editing:** Default for workspace files. The skill applies the smallest supported edits.
- **Working candidate:** Default for immutable sources. The skill returns a complete candidate or exact mechanical changes without modifying the source.
- **No-edit review:** Used when requested. The source remains unchanged.

## What it does

Refine Loop frames the artifact's intent and constraints.
It loads only references needed for evaluation and applies common rules plus one artifact-specific rule set.
It triages every finding and repeats after supported changes.
It stops on `PASS`, unresolved decisions or review defects (`FAIL`), or unusable resources or unsafe conflicts (`BLOCKED`).
The loop permits a maximum of 10 iterations.

The final report identifies the reviewed baseline and delivered candidate when applicable.
It includes applied or proposed changes, remaining issues, user decisions, delegated decisions, and handoff notes.
Only `PASS` means refinement completed.
