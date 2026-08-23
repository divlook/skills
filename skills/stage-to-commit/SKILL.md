---
name: stage-to-commit
description: "Analyze staged changes, choose a Conventional Commit message, and commit immediately."
disable-model-invocation: true
---

# Stage to Commit

Invocation authorizes one immediate commit of exactly the staged snapshot.

## Guardrails

Use only these Git operations:

- `git status --short`
- `git diff --cached --stat`
- `git diff --cached`
- `git log -10 --format=%s`
- `git commit -m`
- `git log -1 --format='%h %s'`

Treat the index and working tree as read-only. The sole write is the authorized
commit. Commit only the snapshot revalidated below.

## Workflow

### 1. Establish a non-empty stage

Run `git diff --cached --stat`.

If it lists no staged path, run `git status --short`, return **Empty stage**,
and stop. Otherwise, retain the stat output for the result.

Complete this step only when the stat identifies at least one staged path.

### 2. Account for the snapshot

Run:

```bash
git diff --cached
git log -10 --format=%s
```

Retain the staged diff as the analyzed snapshot. Read
[commit message conventions](references/commit-conventions.md), then choose one
message that:

- accounts for every staged hunk;
- follows Conventional Commits and the reference;
- matches the recent repository's language and scope style;
- uses a body only when the title cannot cover every significant change.

Complete this step only when every staged hunk maps to the title, body, or
footer without unsupported claims.

### 3. Revalidate the snapshot

Run `git diff --cached` again and compare its complete output byte-for-byte
with the analyzed snapshot.

- **Identical:** continue.
- **Changed:** replace the analyzed snapshot and repeat step 2.
- **Empty:** run `git status --short`, return **Empty stage**, and stop.

Complete this step only when the current staged diff is identical to the diff
used to choose the message.

### 4. Commit and verify

Pass the chosen title and optional body as separate, exactly shell-escaped
`git commit -m` arguments:

```bash
git commit -m "type(scope): subject"
git commit -m "type(scope): subject" -m "body"
```

If the commit fails, return the command error and make no success claim.

On success, run `git log -1 --format='%h %s'`. Return **Result** only when the
displayed subject exactly matches the chosen title.

## Empty stage

```text
The stage is empty.

[git status --short result]

Stage the changes to commit first:
  git add <file>  # Stage a specific file
  git add -p      # Select changes interactively
```

## Result

````text
Analyzed changes:
[git diff --cached --stat result and summary of all staged changes]

Commit message:
```
[Executed commit message]
```

Commit result:
`[git log -1 result]`
````
