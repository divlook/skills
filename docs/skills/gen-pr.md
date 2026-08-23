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

The source defaults to the current branch. The target defaults to the remote default branch. Add the independent `--create` token to authorize immediate creation; otherwise, the skill returns a draft and offers `edit-draft`, `create-pr`, and `stop` as next actions.

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

1. resolves and validates the source and target refs
2. selects the repository pull request template or its built-in minimal template
3. reads every commit and changed path in the three-dot branch diff
4. writes one evidence-backed Conventional Commit-style title and concise body
5. returns the draft or creates the pull request when authorized

Analysis is read-only. It does not fetch, pull, push, or check out branches. Creation requires the GitHub CLI (`gh`) to be installed and authenticated. If an open pull request already exists for the same source and target, updating it requires separate approval.
