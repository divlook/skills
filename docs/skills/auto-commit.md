# Auto Commit

Auto Commit commits changes immediately in logical groups. By default, it commits current-session changes after editing, or the entire working tree in a session that has made no edits. It preserves excluded changes and staging.

## When to use it

Use Auto Commit to commit changes without another confirmation.
It writes English Conventional Commit messages and does not edit file contents.

The skill is model-invoked. Ask the agent to commit or split changes into commits.

```text
Commit the changes.
```

## Commit scope

An explicit scope takes precedence over the defaults:

| Request or session state | Selected scope |
| --- | --- |
| "Commit all changes" without a narrower qualifier | Entire working tree, including other sessions' changes |
| A named file, directory, or session | The specified scope |
| No explicit scope; the session has made edits | Changes identified from the current session's edit history |
| No explicit scope; the session has made no edits | Entire working tree |
| No explicit scope; edit history is lost or incomplete, or editing activity is uncertain | Stop and report missing evidence |

Read-only inspection and discussion do not count as editing. Missing edit records do not prove that the session made no edits.
The entire working tree includes staged, unstaged, and untracked changes reported by Git, not ignored files.
Auto Commit briefly announces the selected scope and its basis, then proceeds without asking for approval.

To include other sessions' changes after working in the current session:

```text
Commit all changes in the entire working tree.
```

To keep a session-only scope, even in a session that has made no edits:

```text
Commit all changes made in this session.
```

An unqualified "all" selects the entire working tree; "all changes made in this session" does not.

To commit exactly the existing staged snapshot instead, use Stage to Commit.

## What it does

1. Selects the explicit or default scope, identifies its changes, and announces the scope without requesting approval.
2. Keeps related implementation, tests, types, configuration, and documentation together.
3. Separates clearly independent tasks and chooses commit messages without a separate plan document.
4. Checks the selected content and commits immediately.
5. Reports created hashes and remaining changes.

It does not start a separate code review or test run. Repository-required checks and commit hooks still apply.

## Shared work and staging

A file changed by this session can also contain another session's edits. File names, timestamps, and staging do not establish ownership.
For a session-only scope, Auto Commit uses the session's edit history to identify changes and stops if ownership cannot be established. Git alone cannot reconstruct session ownership.
For an entire-working-tree scope, it includes other sessions' changes without needing ownership evidence.
It stops if selected and excluded changes cannot be separated, and never expands the scope to include an excluded prerequisite.

Existing staging is reused when it matches the next group. Other staged changes are not cleared.
Partial-file commits and commits with other staged changes use a temporary Git index.
After success, only committed paths are reconciled with the real index. Excluded staged changes remain staged, including those in shared files.
The working-tree content remains unchanged by Auto Commit.

## Stop conditions and limitations

- No changes: report a clean working tree without a commit.
- No authorized changes: report that result and leave other changes untouched.
- Lost or incomplete edit history for a default scope, uncertain editing activity, unidentifiable session-scoped changes, inseparable shared edits, or dependence on excluded uncommitted changes: stop before creating commits.
- Unexpected HEAD, staged-content, or selected-file changes: stop rather than reclassify or include them.
- Commit failure: report the error and current state without automatic edits or retries.

Completed commits remain in place if a later step stops. Hooks can change files or staging even when they fail.
Git does not provide one lock for this multi-command workflow. Detected concurrent changes cause a stop, but uninterrupted cross-session atomicity is not guaranteed.
If a hook changes the temporary index or shared state unexpectedly, stop and report the actual commit and index state.
