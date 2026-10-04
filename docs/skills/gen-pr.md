# Gen PR

Gen PR generates one pull request title and body from the actual changes between two branches. It can return a draft or create the pull request after explicit authorization.

## When to use it

Use Gen PR when you need to:

- draft a pull request from a branch diff
- preserve and fill a repository pull request template
- create a new pull request from the generated draft
- update an existing pull request after separate approval

## How to invoke it

Invoke the skill directly:

```text
/gen-pr [source > target] [--create]
```

The source defaults to the current branch. The target defaults to the remote default branch. Add the independent `--create` token to authorize immediate creation. Otherwise, the skill returns a draft and offers `edit-draft`, `create-pr`, and `stop` as next actions.

Examples:

```text
/gen-pr
```

```text
/gen-pr feature/search > main
```

```text
/gen-pr feature/search > main --create
```

## What it does

Gen PR:

1. resolves and checks the source and target refs
2. checks for uncommitted source changes and whether the remote branch matches the local source commit
3. asks how to continue when commit or push preparation is needed
4. selects the repository pull request template or its built-in minimal template
5. reads every commit and changed path in the three-dot branch diff
6. writes one evidence-backed Conventional Commit-style title and concise body
7. returns the draft or creates the pull request when authorized

### Commit and push preparation

Missing commits or an unpublished source branch prompt a continuation choice rather than an automatic refusal:

- **Prepare and continue:** Approve the needed commit, push, or both. Preparation follows the repository's existing commit workflow. Gen PR adds no commit grouping or message rules.
- **Use existing commits:** Generate a draft from the current branch diff, excluding uncommitted changes. This option is available only when committed changes exist. An unpublished branch can still produce a draft. PR creation waits for publishing approval.
- **Stop:** Finish without preparing changes or writing a PR.

For example, if all intended changes are uncommitted, Gen PR asks whether to commit them and continue. If commits exist locally but have not been pushed, it can draft from them or ask for approval to push before creating the PR. If the remote state cannot be checked, it reports that uncertainty.

`--create` and `create-pr` authorize PR creation only, not commits or pushes. After approved preparation, Gen PR checks readiness again and analyzes the resulting committed snapshot. If it cannot perform preparation, it identifies the missing capability or command error and asks you to complete that work before resuming.

Analysis is read-only. Approved preparation is a separate phase. Gen PR does not fetch, pull, or check out branches. Uncommitted changes are considered only when the requested source is the current branch. If the branch diff is empty and there are no uncommitted source changes, it returns `No changes`.

Creation requires the remote source commit to match the analyzed local commit and the GitHub CLI (`gh`) to be installed and authenticated. If an open pull request already exists for the same source and target, updating it requires separate approval.
