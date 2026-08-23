# Auto Commit

Auto Commit reviews the entire working tree, divides its changes into independently revertible groups, and commits every group immediately.

## When to use it

Use Auto Commit when you want the agent to:

- commit all staged, unstaged, and untracked changes
- split unrelated changes into separate commits
- regroup an existing staged snapshot by logical purpose
- write Conventional Commit messages for each group

## How to invoke it

Ask the agent to commit the current changes or split them into commits. The skill is model-invoked, so no command syntax is required.

Examples:

```text
Commit all current changes.
```

```text
Split the working tree into logical commits and commit them.
```

## What it does

Auto Commit:

1. reads every staged, unstaged, and untracked change
2. assigns every hunk to exactly one logical group
3. orders dependent groups and writes an English Conventional Commit message for each
4. stages and commits each planned group without waiting for another confirmation
5. reports the resulting commit hashes and any changes left behind

Invocation authorizes immediate commits. Auto Commit does not edit file contents. When changes were already staged, it clears only the Git index before rebuilding the planned groups; working-tree content remains intact. Mixed files are split only when their hunk boundaries are clearly separable and each intermediate commit remains valid.
