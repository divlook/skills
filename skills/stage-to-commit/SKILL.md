---
name: stage-to-commit
description: "Analyze staged changes, choose a Conventional Commit message, and commit immediately."
disable-model-invocation: true
---

# Stage to Commit

Invocation authorizes one immediate commit of the analyzed staged snapshot.

The **index** is Git's staging area.
The **staged snapshot** is the recorded content selected for this commit.
The **baseline HEAD** is the commit that must become its sole parent.
A repository without commits has no baseline parent.

## Safety boundaries

Use read-only Git commands to inspect state and commits.
The only authorized command that changes repository state is one `git commit -m` invocation.
Leave file contents and staging unchanged yourself.
Follow repository-required checks and hooks without bypassing them.

Hooks can change files or the index, including when a commit fails.
Git locks protect individual commands, not this entire workflow.
The checks below detect observed changes. They do not guarantee an atomic snapshot across concurrent sessions.
Stop on command errors or detected changes. Do not retry, amend, reset, or automatically undo a commit.

## Procedure

### 1. Record a non-empty snapshot

Run `git rev-parse --verify --quiet HEAD` to record the full baseline commit hash.
If HEAD does not resolve, run `git status --short --branch`.
Continue without a parent only when status identifies a repository without commits.
Stop on other errors.

Record these outputs:

```bash
git diff --cached --raw --no-abbrev --no-renames
git diff --cached --stat
git diff --cached --no-ext-diff --no-textconv
```

The raw diff records paths, file modes, and complete object IDs, including binary content and submodule commits.
Retain its complete output for exact comparisons.
If the raw diff is empty, report **Empty staging** and stop.
Read the complete patch. Retain the stat for the final report.

Read HEAD and the raw diff again with the same commands.
If either differs from its recorded value, report **State changed** and stop.

Complete this step when you read the complete patch and both baseline comparisons match.

### 2. Choose one message

Read `git log -10 --format=%s` when the repository contains commits.
Read [commit message conventions](references/commit-conventions.md) before choosing the message.
Apply repository instructions before recent message conventions.

Complete this step when one message describes the significant staged changes without unsupported claims.

### 3. Check the baseline before committing

Read HEAD and the raw diff again with the same commands from step 1.
Compare each complete output with its recorded baseline.
If HEAD or staged content differs, report **State changed** and stop.
This includes staging that becomes empty. Keep the original baseline instead of analyzing the new state.

Complete this step only when both comparisons match.

### 4. Commit once and check the result

Pass each message paragraph as a separate, exactly shell-escaped `-m` argument.
Choose one command form:

```bash
git commit -m "type(scope): subject"
git commit -m "type(scope): subject" -m "body"
```

Pass a footer as an additional `-m` argument when required.
If the commit fails, report the command error and `git status --short`.
Stop without retrying or claiming success.

Identify the created commit from the successful command output, not from an unverified later HEAD.
Use that commit's hash in these commands:

```bash
git show -s --format='%H%n%P%n%s' <created-hash>
git diff-tree --root --no-commit-id -r --raw --no-abbrev --no-renames <created-hash>
```

Check all these conditions:

- The parent list contains exactly the baseline HEAD, or is empty for a repository without prior commits.
- The complete raw diff exactly matches the recorded raw diff.
- The subject exactly matches the chosen title.
- The current HEAD resolves to the created commit's full hash.

Read `git status --short` to report the observed remaining state.
If any check fails, report **Commit mismatch** and stop.
Include the created hash, failed comparisons, and observed status. Leave the commit in place.
If inspection fails, report the error and known commit hash without claiming success.

Complete this step only when all four checks pass.

## Reports

### Empty staging

Report that no changes are staged.
Include `git status --short` and instructions to stage the intended changes before invoking the skill again.

### State changed

Report which baseline changed and the observed difference.
Include `git status --short`.
Create no commit. Leave the new state untouched.

### Result

Report the analyzed stat and a concise summary of significant changes.
Include the executed message, created hash, and checked subject.
State that content and parent checks passed.
Include the observed remaining status. Do not claim that hooks left files or staging unchanged.
