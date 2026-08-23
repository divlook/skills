# Stage to Commit

Stage to Commit analyzes the current staged snapshot, chooses one Conventional Commit message, and immediately creates exactly one commit.

## When to use it

Use Stage to Commit when:

- the intended commit is already staged
- unstaged and untracked changes must remain untouched
- one commit should represent the complete staged snapshot
- the message should follow the recent repository's language and scope style

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

## What it does

Stage to Commit:

1. confirms that the stage is not empty
2. reads the complete staged diff and recent commit subjects
3. selects one evidence-backed Conventional Commit message
4. revalidates that the staged snapshot has not changed
5. commits that exact snapshot and verifies the resulting subject

Invocation authorizes one immediate commit. The skill does not stage, unstage, or edit files. If the stage is empty, it reports the current Git status and stops. If the staged diff changes during analysis, it analyzes the new snapshot again before committing.
