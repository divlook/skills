---
name: gen-pr
description: "Generate a PR title and body from the actual changes between two branches, and optionally create the PR."
argument-hint: "[current > target] [--create]"
---

# Gen PR

Produce one PR draft per invocation. An independent `--create` token in the current invocation enables create mode; without it, use draft mode. Remove the token first, then parse the remaining input for the source and target branches.

## Guardrails

- Keep analysis read-only: inspect existing refs without fetching, pulling, or checking out branches. Repository writes require the separate preparation approval in Step 2.
- PR writes are one `gh pr create` authorized by `--create` or by selecting `create-pr` after the draft, and one `gh pr edit` separately authorized for an existing PR.
- Ground every claim in the title and body in the diff, commits, or project PR template.
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

For example, when the result is `origin/main`, use `main` as the PR target and `origin/main` as the comparison ref. Ask for the target only when the default branch cannot be discovered. When the user supplies a target, first use that name as the comparison ref; if it does not exist, try `origin/<target>`.

Verify that the source and comparison ref each resolve to a commit:

```bash
git rev-parse --verify --end-of-options "<ref>^{commit}"
```

On failure, identify the invalid ref and ask only for that branch again. This step is complete when both refs resolve and the PR target and comparison ref are identified separately.

### 2. Check commit and push readiness

Inspect the working tree when the source is the current branch, and compare the source commit with the remote branch:

```bash
git status --short --untracked-files=all
git rev-parse --verify --end-of-options "<source>^{commit}"
git ls-remote --heads origin "refs/heads/<source>"
git diff "<comparison>...<source>" --name-status
```

Treat staged, unstaged, and untracked files as uncommitted changes. Changes on a different checked-out branch are outside the requested source. An empty branch diff means there are no committed changes to describe, not that there are necessarily no changes to commit. An absent remote branch or a different remote commit means the source is not published at its local commit. If the remote query fails, report push readiness as unknown rather than assuming it is ready or unpushed.

When the branch diff is empty and there are no uncommitted source changes, output `No changes` with the compared refs and stop.

When uncommitted source changes exist or the source is unpublished or its push readiness is unknown, explain the observed state and ask how to continue:

- **Prepare and continue:** Ask for approval for the specific missing work: commit the intended source changes, push the source, or both. Draft mode needs a commit only when the user wants uncommitted changes included; publishing is needed for PR creation, not for drafting.
- **Use existing commits:** Offer this only when the branch diff is nonempty. State that uncommitted changes are excluded. If publishing is still needed, continue in draft mode and defer PR creation.
- **Stop:** End at the user's request.

Honor the user's choice for the inspected state. Ask again only if that state changes or a later creation request requires publishing.

`--create` and `create-pr` authorize PR creation, not commits or pushes. After preparation approval, use the repository's existing commit workflow and instructions; this skill defines no commit grouping or message rules. Perform only the approved preparation, then rerun the readiness checks before analyzing the snapshot. If preparation cannot be executed, identify the actual missing capability or command error and ask the user to complete that work; resume after it is ready.

This step is complete when a nonempty committed snapshot is available and the user has resolved any preparation choice. Drafting may proceed without a matching remote branch; PR creation requires the remote source commit to match the analyzed local commit.

### 3. Resolve the PR template

Use `read` to check these files in order and use the first one that exists:

1. `.github/pull_request_template.md`
2. `.github/pull_request_template.txt`
3. `.github/PULL_REQUEST_TEMPLATE.md`
4. `.github/PULL_REQUEST_TEMPLATE.txt`
5. `.github/PULL_REQUEST_TEMPLATE/pull_request_template.md`
6. `docs/pull_request_template.md`

If none exists, search file paths once for `pull_request_template`. Use the sole candidate when exactly one exists. When multiple distinct candidates exist and their names do not identify the applicable one, ask the user to choose. When no candidate exists, use the default template in the [PR Writing Contract](references/pr-writing-guidelines.md).

This step is complete when one project template or the default template is selected.

### 4. Describe the change snapshot

Collect these four results:

```bash
git log "<comparison>..<source>" --format='%h %s' --no-merges
git diff "<comparison>...<source>" --stat
git diff "<comparison>...<source>" --name-status
git diff "<comparison>...<source>"
```

If a command fails, return its error and do not produce a draft. If name-status is empty, output `No changes` with the two compared refs and stop.

If the full diff is truncated, read every missing path from name-status with the following command. Escape each path exactly.

```bash
git diff "<comparison>...<source>" -- "<path>"
```

Classify every changed path as added, modified, deleted, renamed, binary, or submodule. Record significant function, class, component, configuration, and documentation changes. Commit messages may help interpret the diff but cannot support a claim that conflicts with it.

This step is complete when every changed path and significant hunk is represented in the description or explicitly classified as insignificant, and the complete diff is retained as the analysis snapshot.

### 5. Write the title and body

Read and apply the complete [PR Writing Contract](references/pr-writing-guidelines.md).

When using a project template, preserve its structure, fixed text, instructions, and checklists. Fill only placeholders supported by evidence, and leave unverified checkboxes unchecked. When using the default template, omit unnecessary sections according to the contract.

Produce one final title and one concise, complete body. Select the title internally and expose only that selection; candidate lists and alternative titles are invalid output. Completion criteria:

- Every concrete claim in the title and body is verified by the analysis snapshot.
- Every significant change is covered with the fewest non-duplicative sentences or bullets.
- No speculation, duplication, empty placeholder, or optional section without reviewer value remains.
- The required structure of the project template remains intact.
- Every title, style, and length rule in the contract is satisfied.

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

For `edit-draft`, apply the requested changes, recheck the Step 5 completion criteria, and show the full title and body. For `create-pr`, continue to the creation procedure below. For `stop`, end without writing a PR.

#### Creation procedure

Recheck Step 2 readiness before creation, including when `create-pr` is selected after a draft. If preparation is needed, obtain its separate approval and complete it first. Rerun the complete diff from Step 4 and compare it with the analysis snapshot. If it changed, repeat Steps 4 and 5 with the new snapshot.

List open PRs with the same source and target:

```bash
gh pr list --head "<source>" --base "<target>" --state open --json url,title,body,baseRefName,headRefName
```

If the lookup fails, return the error instead of guessing whether a PR exists. If it returns an empty array, create the PR and retain the returned URL:

```bash
gh pr create --base "<target>" --head "<source>" --title "<title>" --body "<body>"
```

If one PR exists, show its URL and the new draft, then ask for approval to update it. If multiple PRs exist, ask the user to select one. After approval, edit the selected PR explicitly:

```bash
gh pr edit "<PR URL>" --title "<title>" --body "<body>"
```

After creating or editing, run `gh pr view` with the retained URL. Success requires a present URL and title, body, baseRefName, and headRefName values that exactly match the resolved values. On success, output only the URL and title.

If a command fails or verification differs, do not claim success. Return the error, generated title and body, and the correctly escaped command for manual execution.

## Read-only Git and PR Commands

- `git branch --show-current`
- `git symbolic-ref --short refs/remotes/origin/HEAD`
- `git rev-parse --verify --end-of-options "<ref>^{commit}"`
- `git status --short --untracked-files=all`
- `git ls-remote --heads origin "refs/heads/<source>"`
- `git log "<comparison>..<source>" --format='%h %s' --no-merges`
- `git diff "<comparison>...<source>" --stat`
- `git diff "<comparison>...<source>" --name-status`
- `git diff "<comparison>...<source>"`
- `git diff "<comparison>...<source>" -- "<path>"`
- `gh pr list --head "<source>" --base "<target>" --state open --json url,title,body,baseRefName,headRefName`
- `gh pr create --base "<target>" --head "<source>" --title "<title>" --body "<body>"`
- `gh pr edit "<PR URL>" --title "<title>" --body "<body>"`
- `gh pr view "<PR URL>" --json url,title,body,baseRefName,headRefName`
