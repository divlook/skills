# Stage to Commit

Stage to Commit analyzes staged changes, chooses one Conventional Commit message, and immediately attempts one commit.
It reports success only after checking the created commit's content, parent, and subject.

## When to use it

Use Stage to Commit when:

- the intended commit is already staged
- the agent must leave unstaged and untracked changes untouched
- one commit should represent the complete staged snapshot
- the message should follow repository rules, then recent language and scope conventions

## How to invoke it

Invoke the skill directly after staging the intended changes:

```text
/stage-to-commit
```

Example:

```bash
git add src/search.ts tests/search.test.ts
```

```text
/stage-to-commit
```

Invocation authorizes one immediate commit without another approval request.
The skill runs only through explicit invocation.

## What it does

1. Records HEAD and staged paths, file modes, and complete object IDs.
2. Reads the complete staged patch and recent commit subjects.
3. Chooses a message that describes significant changes, without listing every hunk or incidental cleanup.
4. Checks that HEAD and staged content still match the recorded baseline.
5. Attempts one commit with normal repository checks and hooks.
6. Checks the created commit's content, parent, subject, and correspondence with current HEAD.

Object IDs identify content, including binary files and submodule commits.
For a repository without commits, the expected parent list is empty.

## Stop conditions and limitations

The agent does not stage, unstage, or edit files.
If staging is initially empty, it reports Git status and asks you to stage the intended changes.
If HEAD or staged content changes during analysis, it reports the difference and stops before committing.
It does not analyze or commit the new state automatically. Invoke the skill again after checking that state.

Hooks can change files or staging, even when the commit fails.
A failed commit reports the command error and observed status without a retry or success claim.
A commit mismatch reports the created hash, failed comparisons, and observed status.
The skill leaves that commit in place. It does not automatically amend, reset, or undo it.

These checks detect observed changes, not prevent concurrent changes.
Git locks do not make the entire workflow atomic across sessions.

## Successful result

The result includes:

- the analyzed change stat and a concise summary of significant changes
- the executed message
- the created commit hash and checked subject
- confirmation that content and parent checks passed
- the observed remaining Git status

The result does not promise that hooks left files or staging unchanged.
