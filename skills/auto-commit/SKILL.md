---
name: auto-commit
description: "Use when asked to commit changes or split changes into commits. Commit current-session changes immediately. Include the entire working tree only when explicitly requested."
---

# Auto Commit

Commit changes made in the current session. Invocation authorizes immediate commits within that scope, not other sessions' changes.

## Procedure

### 1. Identify the authorized changes

Inspect repository state:

```bash
git status --short --untracked-files=all
git diff --cached --name-status
```

- Use the session's edit history and recorded before-and-after content to identify its changes.
- An explicit user scope takes precedence. Include all sessions' changes only when the user explicitly requests the entire working tree.
- A filename, directory, timestamp, or staged state does not establish which session made a change.
- Read candidate patches once with `git diff -- <paths>` and `git diff --cached -- <paths>`.
- Inspect untracked content only when it is within the authorized scope.
- Distinguish authorized staged changes from authorized unstaged changes. Preserve excluded changes and their staged state.
- For shared files, read [shared-state handling](references/shared-state.md) before selecting changes.
- If ownership or separation is uncertain, stop before creating commits. Report the affected paths and missing evidence.
- If there are no changes, report a clean working tree and stop.
- If there are no authorized changes, report that result. Leave other changes untouched.

Complete this step when every authorized change has scope evidence and a recorded staged or unstaged baseline. Record HEAD too.

### 2. Choose groups and messages

Keep one task's implementation, tests, types, configuration, and documentation together. Split only clearly independent tasks.

- Read the [grouping criteria](references/grouping-strategy.md) only when task boundaries or dependencies are unclear.
- Keep overlapping changes from authorized tasks together. Never include excluded changes to make a group complete.
- Stop if an authorized group requires excluded uncommitted changes to work.
- Order commits so that later commits depend only on earlier commits.
- Keep group membership and messages internally. Do not write a separate execution-plan document or request routine confirmation.

Apply the [commit message conventions](references/commit-conventions.md) to choose each subject and any necessary body.

Complete this step when each authorized change belongs to one group with a concrete message and a valid dependency order.

### 3. Commit each group

Process groups in order:

1. Compare HEAD, staged content, and selected working-tree content with their recorded baseline before staging or committing.
2. If any differs unexpectedly, stop. Report completed commits and changed paths. Do not absorb new changes or repeat classification.
3. If other changes are staged, use [shared-state handling](references/shared-state.md). Use it for partial-file commits too.
4. Otherwise, reuse the index when it contains exactly this group. Stage whole-file changes with `git add -- <paths>` only when each file contains only this group's changes.
5. Read the commit index's `git diff --cached`. Confirm that it contains exactly the group and matches the inspected content.
6. Commit with `git commit -m "type(scope): English subject"`. Pass an optional body as a separate `-m` argument.
7. If a commit fails, stop. Report the error and current state. Make no further file or index changes and do not retry.
8. Confirm the created commit matches the inspected group and expected parent. Compare selected working-tree content with its baseline.
9. Confirm the real index contains the expected remaining staged content. Stop on unexpected hook or concurrent changes.
10. Retain the hash. Refresh only HEAD and index baselines changed by this commit or its required reconciliation. Preserve working-tree baselines.

Complete this step when each selected group became one commit containing no excluded changes.

### 4. Report the result

Inspect the final status and created commits:

```bash
git status --short --untracked-files=all
git log --oneline -N
```

`N` is the number of commits created. Skip the log command when none were created.
Report each hash and subject in order. Report remaining paths and why they were excluded or left uncommitted.

Complete this step when the created commits and remaining changes are accounted for.

## Safety boundaries

- Keep the index and working tree unchanged until scope and groups are established.
- Leave file contents unchanged.
- Preserve excluded staged changes. Never reset the entire index.
- Do not start a separate code review or test run. Follow repository-required checks and commit hooks without bypassing them.
- Hooks can change files or the index, including on failure. Report observed state rather than promising an unchanged index.
- Git locks protect individual operations, not this workflow. Stop on detected concurrent changes. Do not claim cross-session atomicity.
