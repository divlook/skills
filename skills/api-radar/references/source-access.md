# Source Access

Choose the branch established by `Resolve the Input` and keep the source immutable.

## Local source

Use filesystem-native tools to read, list, and search the working tree. Use `git` only for repository metadata, history, diffs, and content at an unmounted revision.

Useful read-only operations:

```bash
git show {revision}:{path}
git diff {base}...{target} -- {path}
git log --oneline --all -- {path}
```

Resolve a requested branch or commit with `git rev-parse`. Local analysis needs neither GitHub authentication nor a GitHub remote.

## GitHub source

Use the GitHub CLI (`gh`) for repository content and metadata. Use read operations only.

Useful operations:

```bash
gh repo view {owner}/{repo} --json defaultBranchRef,nameWithOwner
gh search code "{query}" --repo {owner}/{repo} --limit 100
gh api repos/{owner}/{repo}/commits/{ref} --jq '.sha'
gh api "repos/{owner}/{repo}/contents/{path}?ref={ref}"
gh pr view {number} --repo {owner}/{repo} --json number,title,body,author,state,baseRefName,headRefName,files,url
gh pr diff {number} --repo {owner}/{repo}
gh api "repos/{owner}/{repo}/compare/{base}...{head}"
```

Run the required read operation directly. If authentication blocks it, state the failed source. Ask the user to run `gh auth login`. Resume remote analysis only after evidence is accessible.
