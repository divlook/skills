# Source Access

Use the access branch established by `Resolve the Input`. Leave the source repository unchanged.

## Comparison meaning

Record the comparison start and target as commit SHAs:

| Request | Comparison start | Target |
|---------|------------------|--------|
| PR or branch changes | Common ancestor of base and head | Head commit |
| Commit changes | Parent commit | Requested commit |
| Explicit revision state comparison | Requested start commit | Requested target commit |

For branch changes, use the requested base or the repository's established base. Ask when the evidence leaves the base ambiguous.
For a merge commit, ask which parent to use unless the request identifies it.
For a root commit, compare the empty tree with the commit.
Use the recorded start for both the change list and previous endpoint behavior.

## Evidence at a revision

Read files at the resolved SHA, not at a moving branch name.
Search results locate candidates. They do not establish evidence at the selected revision.
Inspect routing locations at that revision to find candidates absent from search results.

## Local source

Use filesystem-native tools to read, list, and search the working tree.
Use `git` for repository metadata, history, diffs, and content at an unmounted revision.

Useful read-only operations:

```bash
git rev-parse --verify "{ref}^{commit}"
git merge-base {baseSHA} {headSHA}
git show {commitSHA}:{path}
git diff {startSHA} {targetSHA} -- {path}
git log --oneline --all -- {path}
```

Resolve the common ancestor before the diff for PR or branch changes.
The direct diff then uses the recorded comparison start and target.
Local analysis needs neither GitHub authentication nor a GitHub remote.

## GitHub source

Use the GitHub CLI (`gh`) for repository content and metadata. Use read operations only.

Useful operations:

```bash
gh repo view {owner}/{repo} --json defaultBranchRef,nameWithOwner
gh search code "{query}" --repo {owner}/{repo} --limit 100
gh api repos/{owner}/{repo}/commits/{ref} --jq '.sha'
gh api "repos/{owner}/{repo}/contents/{path}?ref={commitSHA}"
gh api "repos/{owner}/{repo}/git/trees/{commitSHA}?recursive=1"
gh pr view {number} --repo {owner}/{repo} --json number,title,body,author,state,baseRefName,baseRefOid,headRefName,headRefOid,headRepository,headRepositoryOwner,url
gh api --paginate "repos/{owner}/{repo}/compare/{baseSHA}...{headSHA}"
```

For PRs, record metadata and base and head SHAs from the same response.
The compare response supplies `merge_base_commit.sha` for PR or branch changes.
Its file list compares the common ancestor with the head.
For an explicit state comparison, compare files at the two requested SHAs instead of using that file list.
Compare responses can limit changed files. Recursive trees can be truncated.
Use directory content reads at the pinned SHAs to complete missing file coverage.
If coverage remains incomplete, record the missing scope as partial analysis.

For fork PRs, identify the head repository from PR metadata.
Read head files there at the recorded head SHA. Read previous files from the base repository at the comparison start.
Treat these as the two sides of the same PR source, not as unrelated source selections.

Run the required read operation directly. If authentication blocks it, state the failed source.
Ask the user to run `gh auth login`. Resume remote analysis only after evidence is accessible.
