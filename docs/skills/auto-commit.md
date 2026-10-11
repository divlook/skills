# Auto Commit

Auto Commit commits current-session changes immediately. It separates clearly independent tasks and preserves other sessions' changes and staging.

## When to use it

Use Auto Commit to commit work from the current session without another confirmation.
It writes English Conventional Commit messages and does not edit file contents.

The skill is model-invoked. Ask the agent to commit or split changes into commits.

```text
Commit the changes made in this session.
```

An explicit scope takes precedence. To include every session's changes, request the entire working tree:

```text
Commit all staged, unstaged, and untracked changes in the entire working tree.
```

To commit exactly the existing staged snapshot instead, use Stage to Commit.

## What it does

1. Uses session edit history and recorded content to identify authorized changes.
2. Keeps related implementation, tests, types, configuration, and documentation together.
3. Separates clearly independent tasks and chooses commit messages without a separate plan document.
4. Checks the selected content and commits immediately.
5. Reports created hashes and remaining changes.

It does not start a separate code review or test run. Repository-required checks and commit hooks still apply.

## Shared work and staging

A file changed by this session can also contain another session's edits. File names, timestamps, and staging do not establish ownership.
Auto Commit selects only changes supported by the session's edit history. It stops if ownership or separation is uncertain.
An agent with no retained edit history cannot reconstruct session ownership from Git alone.

Existing staging is reused when it matches the next group. Other staged changes are not cleared.
Partial-file commits and commits with other staged changes use a temporary Git index.
After success, only committed paths are reconciled with the real index. Excluded staged changes remain staged, including those in shared files.
The working-tree content remains unchanged by Auto Commit.

## Stop conditions and limitations

- No changes: report a clean working tree without a commit.
- No authorized changes: report that result and leave other changes untouched.
- Uncertain ownership, inseparable shared edits, or dependence on excluded uncommitted changes: stop before creating commits.
- Unexpected HEAD, staged-content, or selected-file changes: stop rather than reclassify or include them.
- Commit failure: report the error and current state without automatic edits or retries.

Completed commits remain in place if a later step stops. Hooks can change files or staging even when they fail.
Git does not provide one lock for this multi-command workflow. Detected concurrent changes cause a stop, but uninterrupted cross-session atomicity is not guaranteed.
If a hook changes the temporary index or shared state unexpectedly, stop and report the actual commit and index state.
