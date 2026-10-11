---
name: gen-pr
description: "Generate a PR title and body from the actual changes between two branches, and optionally create the PR."
argument-hint: "[current > target] [--create]"
---

# Gen PR

Produce one PR draft per invocation. An independent `--create` token in the current invocation enables create mode.
Without it, use draft mode. Strip the token first. Parse the remaining input for the source and target branches.

## Guardrails

- Keep analysis read-only. Require separate approval for commits, pushes, and target fetches. Do not pull or check out branches.
- Authorize one `gh pr create` with `--create` or the `create-pr` selection after the draft.
- Require separate approval for one `gh pr edit` of an existing PR.
- Describe committed changes only. A project template defines format, not evidence that a change or check occurred.
- Pass branch names and generated text as separate, correctly shell-escaped arguments. Do not compose generated text into shell syntax.

## Workflow

### 1. Resolve the branches

When the request omits the source, use the current branch:

```bash
git branch --show-current
```

An empty result means detached HEAD. Ask for the source branch.

When the request omits the target, discover the remote default branch:

```bash
git symbolic-ref --short refs/remotes/origin/HEAD
```

Ask for the target only when the default branch cannot be discovered.

Keep these values distinct:

- **PR target:** The destination branch name on GitHub, such as `main`.
- **Comparison ref:** The Git ref used to calculate changes, normally `refs/remotes/origin/<target>`.
- **Analysis snapshot:** The source commit ID, comparison commit ID, and complete diff between them.

For `origin/main`, set the PR target to `main`. Use `refs/remotes/origin/main` as the comparison ref.
Use the same remote comparison rule when the user supplies `main`. Do not substitute the local `main` branch.

Use a local comparison ref only when the user explicitly requests local branch comparison in draft mode.
Label that output as a local comparison draft.
In create mode, use the remote comparison and explain any switch from a local comparison request.
Read Section 1 of the [PR Creation Procedure](references/pr-creation.md) before checking for an empty committed diff.
Apply that section only in create mode. Initial target resolution returns here before Step 2.

Check that the source and comparison ref each resolve to a commit:

```bash
git rev-parse --verify --end-of-options "<ref>^{commit}"
```

On source failure, identify the invalid ref and ask only for the source again.
If the remote comparison ref is missing in draft mode, report its exact name.
Draft mode makes no remote query or automatic fetch.
Ask for a different target or for the missing ref to be prepared before resuming.
Do not fall back to a local target.
For an invalid explicit local comparison ref, ask only for that ref again.

Complete this step when both refs resolve and the comparison mode is identified.

### 2. Resolve uncommitted source changes

Inspect the working tree only when the source is the current branch:

```bash
git status --short --untracked-files=all
```

Inspect the committed diff for every source:

```bash
git diff "<comparison>...<source>" --name-status
```

Treat staged, unstaged, and untracked files as uncommitted changes.
Changes on another checked-out branch are outside the requested source.
An empty branch diff means there are no committed changes to describe. Uncommitted changes can still exist.

If the committed diff is empty and no uncommitted source changes exist, output `No changes` with the compared refs.
Stop without producing a draft.

If source changes are uncommitted, explain the observed state. Ask whether to include them before writing the draft:

- **Use existing commits:** Offer this only when the committed diff is nonempty. State that the draft excludes uncommitted changes.
- **Commit selected changes and include them:** Identify the intended changes and exact commit scope. Request explicit approval for that scope before committing. This selection does not authorize every uncommitted change or a push.
- **Stop:** End at the user's request.

Honor the user's choice while the inspected changes remain unchanged. Ask again if those changes change.
After commit approval, follow the repository's existing commit workflow. This skill defines no commit grouping or message rules.
Commit only the approved changes. Recheck status and the committed diff after preparation.
Treat previously inspected, unselected changes as excluded unless they changed.
Approved commits resolve the selected changes without another inclusion question.
If preparation fails, report the actual command error or missing capability.
Ask the user to complete the approved work before resuming.

Without uncommitted source changes, proceed without a preparation question. Draft mode does not query remote publication status.
Complete this step when a nonempty committed diff exists and every applicable inclusion choice is resolved.

### 3. Resolve the PR template

Use `read` to check these files in order and use the first one that exists:

1. `.github/pull_request_template.md`
2. `.github/pull_request_template.txt`
3. `.github/PULL_REQUEST_TEMPLATE.md`
4. `.github/PULL_REQUEST_TEMPLATE.txt`
5. `.github/PULL_REQUEST_TEMPLATE/pull_request_template.md`
6. `docs/pull_request_template.md`

If none exists, search file paths once for `pull_request_template`.
Use the sole candidate when exactly one exists.
Ask the user to choose when multiple distinct candidates exist and their names do not identify the applicable one.
With no candidate, use the default template in the [PR Writing Contract](references/pr-writing-guidelines.md).

Complete this step when you select one project template or the default template.

### 4. Describe the change snapshot

Resolve both refs to commit IDs before collecting evidence:

```bash
git rev-parse --verify --end-of-options "<source>^{commit}"
git rev-parse --verify --end-of-options "<comparison>^{commit}"
```

Record these as `source_commit` and `comparison_commit`. Use those fixed IDs for every command in this step:

```bash
git log "<comparison_commit>..<source_commit>" --format='%h %s' --no-merges
git diff "<comparison_commit>...<source_commit>" --stat
git diff "<comparison_commit>...<source_commit>" --name-status
git diff "<comparison_commit>...<source_commit>"
```

If a command fails, return its error and do not produce a draft.
If name-status is empty, output `No changes` with the compared refs and stop.

If the full diff is truncated, read every missing path from name-status with this command. Escape each path exactly.

```bash
git diff "<comparison_commit>...<source_commit>" -- "<path>"
```

Classify every changed path as added, modified, deleted, renamed, binary, or submodule.
Record significant function, class, component, configuration, and documentation changes.
Commit messages may help interpret the diff but cannot support a claim that conflicts with it.
Account for every changed path and significant hunk. Describe each or explicitly classify it as insignificant.
Retain both commit IDs and the complete diff as the analysis snapshot.

Complete this step when every path is accounted for and all evidence uses the recorded IDs.

### 5. Write the title and body

Read and apply the complete [PR Writing Contract](references/pr-writing-guidelines.md).
Produce one title and one body from the analysis snapshot.
Complete this step when the draft satisfies the contract's completion criteria.

### 6. Deliver the draft or write the PR

#### Draft mode

Present the draft in this format:

````text
PR title:
```
[Title]
```

PR body:
```
[Body]
```

Next action:
- `edit-draft`: Request changes to the title or body
- `create-pr`: Create the PR from this draft
- `stop`: Finish without creating a PR
````

State when uncommitted changes were excluded. Identify local comparison drafts.
For remote comparison drafts, state that the local remote-tracking ref's freshness was not checked.
For `edit-draft`, apply the requested changes and repeat the Step 5 contract check. Show the full title and body.
For `create-pr`, follow the creation procedure below. For `stop`, end without writing a PR.

#### Creation procedure

For `--create` or `create-pr`, read and follow the complete [PR Creation Procedure](references/pr-creation.md).
Complete delivery when the full draft meets Step 5 or the creation procedure verifies the retained PR.
