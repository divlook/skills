---
name: auto-commit
description: "Use when asked to commit changes or split changes into commits. Review the entire working tree. Group the changes into logical units and commit immediately."
---

# Auto Commit

Review every staged, unstaged, and untracked change. Commit them as independent units.

## Procedure

### 1. Freeze the change inventory

Inspect:

```bash
git status --short --untracked-files=all
git diff --stat
git diff --cached --stat
```

- Read the complete `git diff` and `git diff --cached` patches for tracked changes.
- Open every untracked path and inspect its contents. For binaries, record the format and purpose instead.
- Track the staged and unstaged patches separately for files that contain both.
- If there are no changes, report that the working tree is clean and stop.

Complete this step only when you account for every changed path and patch as staged, unstaged, or untracked.

### 2. Design change groups

Apply the [grouping criteria](references/grouping-strategy.md) to divide the changes into groups that can be reverted independently.

- Keep changes together when they only become complete as a unit. Examples include an implementation with its tests, types, or configuration.
- If one file contains hunks from multiple tasks, mark it as a mixed change. Split only clearly separable hunks with `git add -p`. Keep overlapping syntax units in one group. Keep changes together if separation would break them.
- If staged changes exist, plan to regroup all staged and unstaged changes into the logical groups defined here.
- Keep the index unchanged during group design. Apply the existing-index procedure in step 4 only after you finalize the plan.
- Order commits so that later commits depend only on earlier commits.

Apply the [commit message conventions](references/commit-conventions.md) to write each group's subject and any necessary body.

Complete this step only when every changed hunk belongs to exactly one group. Each group must have a subject that explains it on its own.

### 3. Finalize the execution plan

Finalize the plan as an internal checklist in this format:

```text
Changed files: N (modified M, added A, deleted D)

1. Group description
   - Files: path (status)
   - Included changes: concrete behavior
   - Commit: type(scope): English subject

Warnings:
- Mixed changes, existing staged changes, generated files, or other items that affect execution
```

Continue immediately to step 4 after you finalize the checklist.

Complete this step only when you fix every hunk's group, execution order, commit message, and existing-index handling.

### 4. Commit each group

Process each group in the planned order.

If the inventory contained staged changes, clear only the index with `git reset HEAD -- .`. Run it immediately before staging the first group. Preserve all working-tree content. Regroup all staged and unstaged changes into the planned groups.

1. Stage ordinary groups with `git add -- <paths>`.
2. Use `git add -p -- <path>` only for the clearly separable hunk boundaries fixed in step 2.
3. Read `git diff --cached --name-status` and `git diff --cached` to verify that the index contains only the current group.
4. Commit with `git commit -m "type(scope): English subject"`. Add the body as a separate `-m` argument.
5. If a commit fails, stop before processing the next group. Report the error and leave the current index unchanged.

Completion criterion: Every planned group became exactly one commit, and the pre-commit index inspection contained no changes from another group.

### 5. Verify the result

```bash
git status --short --untracked-files=all
git log --oneline -N
```

`N` is the number of commits created. Report each resulting hash and subject in order. If changes remain, report their paths. Explain why you left each change uncommitted.

Complete this step only when the number of created commits matches the log. Account for every remaining working-tree change.

## Safety boundaries

- Keep the index and working tree unchanged until you finalize the inventory and execution plan.
- Leave file contents unchanged.
- Stage and commit only changes included in the planned groups.
- Apply the existing-index procedure only as specified in step 4.
