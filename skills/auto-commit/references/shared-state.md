# Shared-State Handling

Use this procedure for partial-file commits or when the real index contains changes outside the next group.
The real index is the repository's existing staging area. A temporary index selects commit content without clearing that staging area.

## Select the changes

Use recorded edits to separate the group's changes from excluded changes.
Record two selections:

- The group's complete patch against HEAD, including its staged and unstaged changes.
- Only the group's unstaged patch against the real index.

Keep original patch context. Check applicability with `git apply --cached --check` against the intended temporary index.
Do not discard context, use three-way conflict resolution, or include excluded lines to force a patch to apply.
Stop if edits overlap, patch context requires excluded changes, or a valid separation cannot be demonstrated.
Whole-file binary changes, additions, deletions, and renames require evidence that the selected unit contains no excluded changes.

## Prepare isolated indexes

1. Record HEAD, the real index tree from `git write-tree`, and selected working-tree content.
2. Stop if the index has unresolved conflicts or selected paths require index flags that this procedure cannot preserve.
3. Create a temporary directory outside the working tree. Choose two unused absolute index paths inside it.
4. Initialize the commit index from the recorded HEAD with `GIT_INDEX_FILE=<commit-index> git read-tree <head>`.
5. Apply the group's complete patch with `GIT_INDEX_FILE=<commit-index> git apply --cached <group-patch>`.
6. Initialize the reconciliation index from the recorded real index tree with `GIT_INDEX_FILE=<reconciliation-index> git read-tree <index-tree>`.
7. Apply only the selected unstaged patch to the reconciliation index. Skip this operation if all selected changes were already staged.

The commit index now contains only this group above HEAD.
The reconciliation index contains the original staged content plus the group's previously unstaged changes.
It retains excluded staged changes, even in a shared file.

For a whole-file change, `git add -- <paths>` can replace patch application in either temporary index.
Use this shortcut only when every change in those paths belongs to this group.
For an unborn branch, initialize the commit index with `git read-tree --empty` instead. Record HEAD as absent.
Run each temporary-index command with its own `GIT_INDEX_FILE` environment value. Do not export it for unrelated commands.

Complete preparation when both indexes contain their intended content and every selected path has a known reconciliation entry.

## Commit and reconcile

1. Compare current HEAD, real index tree, and selected working-tree content with the recorded state. Stop on any unexpected difference.
2. Inspect the commit index's complete `git diff --cached`. Record its tree with `git write-tree` under the same index environment.
3. Run `git commit -m` with `GIT_INDEX_FILE=<commit-index>`. Do not disable repository hooks or signing requirements.
4. On failure, report the error and actual state. Do not reconcile the real index or retry.
5. On success, inspect the created commit. Confirm its parent is the recorded HEAD and its tree matches the inspected commit index tree.
6. Before reconciliation, confirm HEAD is still that commit, the real index tree is unchanged, and selected working-tree content is unchanged.
7. If a hook or another session changed any of these values, stop. Report the created commit and the unreconciled staging state.
8. Update only this group's paths in the real index from the prepared reconciliation index.

Read reconciliation entries with `GIT_INDEX_FILE=<reconciliation-index> git ls-files --stage -z -- <paths>`.
Feed those entries to the real index through `git update-index -z --index-info` without `GIT_INDEX_FILE`.
For a deleted path, supply a mode-zero entry with a zero object ID and the path. Use NUL-delimited paths throughout.
Include both old and new paths for a rename. Never replace the entire real index with the temporary index.

Confirm the real index tree now equals the prepared reconciliation tree.
Compare selected working-tree content again. Stop if either check detects an unexpected change.
The selected changes are now committed. Excluded staged changes remain staged relative to the new HEAD.

Complete this step when the commit contains exactly the selected group and the real index retains the prepared staged content.

## Interruptions

A successful isolated commit advances HEAD before real-index reconciliation.
If execution stops in that interval, the commit remains valid, but the real index can show reverse changes for the committed paths.
Report that state explicitly. Do not describe it as preserved staging or silently repair it after a detected conflict.

Remove only temporary files created by this procedure when they are no longer needed.
On a stopped reconciliation, retain the prepared index paths for an explicit recovery request.
Git locks serialize individual operations, not the entire sequence. State comparisons detect changes but do not eliminate concurrent-write races.
