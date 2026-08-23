---
name: auto-commit
description: "When asked to commit changes or split them into commits, review the entire working tree, group changes into logical units, and immediately commit the resulting groups."
---

# Auto Commit

Review every staged, unstaged, and untracked change, then commit them as independent units.

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

Completion criterion: Every changed path and patch in the status output is accounted for as staged, unstaged, or untracked.

### 2. Design change groups

Apply the [grouping criteria](references/grouping-strategy.md) to divide the changes into groups that can be reverted independently.

- Keep changes together when they only become complete as a unit, such as an implementation and its tests, types, or configuration.
- If one file contains hunks from multiple tasks, mark it as a mixed change. Split only clearly separable hunks with `git add -p`; keep overlapping syntax units or changes that would break when separated in one group.
- If staged changes exist, plan to regroup all staged and unstaged changes into the logical groups defined here.
- Do not clear the index during group design. Run `git reset HEAD -- .` immediately before staging the first group, preserving all working-tree content.
- Order commits so that later commits depend only on earlier commits.

Apply the [commit message conventions](references/commit-conventions.md) to write each group's subject and any necessary body.

Completion criterion: Every changed hunk belongs to exactly one group, and each group has a subject that explains it on its own.

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

Do not wait for another user response. Continue immediately to step 4 after finalizing the checklist.

Completion criterion: Every hunk's group, execution order, commit message, and existing-index handling are fixed before step 4 begins.

### 4. Commit each group

Process each group in the planned order.

If the inventory contained staged changes, clear only the index with `git reset HEAD -- .` immediately before staging the first group, then regroup all changes in the working tree.

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

`N` is the number of commits created. Report each resulting hash and subject in order. If changes remain, report their paths and why they were left behind.

Completion criterion: The number of created commits matches the log, and every remaining working-tree change is accounted for.

## Safety boundaries

- Keep the index and working tree unchanged until the inventory and execution plan are finalized.
- Leave file contents unchanged.
- Stage and commit only changes included in the planned groups.
- Apply reset only to the index with `git reset HEAD -- .`; preserve all working-tree content.
