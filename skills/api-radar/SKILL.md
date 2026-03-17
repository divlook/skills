---
name: api-radar
description: "Analyzes REST API endpoints in any GitHub repository (read-only) and generates structured API documentation as Endpoint Reference Cards. Supports Django, FastAPI, Express, NestJS, Spring and more. Find endpoints by path, keyword, or natural language — or analyze API changes in PRs, commits, and branches."
---

# API Radar

Analyzes API endpoints in a GitHub repository **read-only** and produces reference documentation.
Never creates, modifies, or deletes any code, file, branch, or PR — only analysis and documentation.

## Input Format

Users make requests in the following format:
- `[repo] [query]`

`repo` is one of:
- `owner/repo` (recommended)
- GitHub repository URL (e.g. `https://github.com/example-owner/example-repo`)
- alias (if registered in [references/repo-aliases.md](references/repo-aliases.md))

`query` is one of:
- API path (e.g. `/v1/users/{user_id}`)
- keyword
- description (in any language)
- PR / commit / branch reference

Input examples:

| Type | Example | Description |
|------|---------|-------------|
| API path | `example-owner/example-repo /companies/{company_id}/users` | When the exact path is known |
| Keyword | `example-owner/example-repo partner` | When part of an endpoint/identifier is known |
| Description | `example-owner/example-repo file upload API` | When only the functionality is known |
| Repo URL | `https://github.com/example-owner/example-repo /v1/health` | Specifying repo by URL |
| PR number | `example-owner/example-repo PR#123` | Analyze API changes in a specific PR |
| Commit | `example-owner/example-repo commit 1a2b3c4` | Analyze based on a specific commit |
| Branch | `example-owner/example-repo branch feature/file-upload` | Analyze based on a specific branch |
| Alias | `my-api /v1/health` | Specify repo by alias |

If the repo cannot be identified from the input (e.g. missing repo, typo, URL parse failure), always ask the user for clarification.

## Core Workflow

### Step 0: Determine Search Mode

Check whether the input contains a PR, commit, or branch keyword.

| Keyword | Search Method |
|---------|--------------|
| PR #number, PR keyword | Retrieve PR metadata/changes via `gh` |
| commit keyword | Retrieve commit via `gh` |
| branch name | Retrieve branch/files via `gh` |
| Other | String-based search (best-effort) |

#### GitHub CLI Auth Check

Before analyzing PR/commit/branch, verify authentication status:

```bash
gh auth status
```

If not authenticated, output the following (do not perform authentication yourself):

```
⚠️ GitHub CLI authentication required.
Please run 'gh auth login' in your terminal to complete authentication.
```

### Step 1: Search Endpoints

#### Default Search (best-effort)

Search strategy by input type:

[When the API path is known]
- Query: `@router.get("/companies")` or `path("companies/")`
- Find endpoint code directly with the exact path

[When only a keyword is known]
- Query: `partner` `PartnerViewSet` `partner_router`
- Explore candidate ViewSets, Routers, and API files

[When only a description is known]
- 1st search: original keywords
- 2nd search: English translation / alternative terms
- 3rd search: domain-specific terminology (e.g. `login`, `payment`, `upload`)
- Present a list of related endpoints first, then do a detailed analysis after the user selects

[When multiple results are found]
- Present related APIs in a table
- Guide the user to select which API to analyze

Example output:

| # | Path | Method | Description |
|---|------|--------|-------------|
| 1 | /files | POST | Upload a file |
| 2 | /files/{id} | GET | Retrieve file metadata |
| 3 | /files/{id}/download | GET | Download file content |

Please select the API number to analyze.

#### PR/Commit/Branch Search (GitHub CLI)

[When PR number is known]

```bash
gh pr view {PR_NUMBER} --repo {owner}/{repo}
gh pr view {PR_NUMBER} --repo {owner}/{repo} --json files,title,body,author
gh pr diff {PR_NUMBER} --repo {owner}/{repo}
```

[PR keyword search]

```bash
gh pr list --repo {owner}/{repo} --search "{keyword}" --state open --limit 10
gh pr list --repo {owner}/{repo} --search "{keyword}" --state merged --limit 10
```

Example output:

| # | PR | Title | Author | Status |
|---|----|-------|--------|--------|
| 1 | #456 | feat: add file upload API | @developer | Open |
| 2 | #423 | fix: file size validation error | @developer | Merged |

Please select the PR number to analyze.

[Commit message search]

```bash
gh search commits --repo {owner}/{repo} "{keyword}" --limit 10
```

[Analyze specific branch code]

```bash
gh api repos/{owner}/{repo}/contents/{file_path}?ref={branch_name} --jq '.content'
```

Note:
- `.content` is base64-encoded and must be decoded.
- On macOS use `base64 -D`; on GNU systems `base64 -d` may work.
- When unsure, use `python3`:

```bash
python3 - <<'PY'
import base64, sys
print(base64.b64decode(sys.stdin.read()).decode('utf-8', errors='replace'))
PY
```

Comparing branch with default branch (list of changed files):

```bash
gh repo view --repo {owner}/{repo} --json defaultBranchRef --jq '.defaultBranchRef.name'
gh api repos/{owner}/{repo}/compare/{defaultBranch}...{branch} --jq '.files[].filename'
```

### Step 2: Repo Profiling

For each request, quickly identify the backend framework/routing/schema hints in the repository on a best-effort basis, then maintain the same analysis flow (search → auth/permission → errors → Endpoint Card documentation).

Refer to [references/framework-detection.md](references/framework-detection.md) for framework-specific detection hints.

Do not stop work even if the framework/routing/schema cannot be confirmed.
- Continue with Endpoint Card documentation using the same template/flow.
- Leave uncertain or unconfirmed items in the `#### Uncertainties` section of the output, along with evidence (search terms / candidate file paths / reasoning).

### Step 3: Extract Auth/Permission

Common authentication methods (project-specific — check actual implementation):
- API Key (e.g. `X-API-Key: {key}` header)
- JWT Bearer token (e.g. `Authorization: Bearer {token}`)
- Session / Cookie-based auth

Token payload and session claims vary by project — extract only observed fields (e.g. `user_id`, `role`, `scope`).

Permission info extraction (varies by framework/project):
- Decorator-based: e.g. `@require_permissions("read:files")`, `@permission_required("admin")`
- Middleware-based: e.g. role/scope checks in auth middleware
- Inline checks: e.g. `if not user.has_perm("app.change_file"): raise PermissionDenied`

Record only **observed** permission patterns — note the source file and mechanism.

### Step 4: Analyze Errors

Error response formats vary by API/project, so do not assume a "common format".

Rules (Observed-first):
- Only describe things **actually observed** from status codes/exception classes/handlers/common processing (e.g. middleware, exception filter/handler) as `Errors (Observed)`.
- Since error body/field names/code schemes vary by project, only record actually confirmed fields/values.
- Leave unobservable/uncertain items as `unknown`, with evidence (search terms used + candidate file paths).

Information extractable from exception/error definitions (field names vary by project):
- status_code: HTTP status code (400, 401, 403, 404, etc.)
- error_code: value corresponding to business error code (e.g. `error_code`, `code`, `errorCode`, etc.)
- message: value corresponding to user message (e.g. `message`, `error_message`, `detail`, etc.)

### Step 5: Generate Output

Document the analysis result according to the output template.

- General API analysis: [references/endpoint-card-template.md](references/endpoint-card-template.md)
- PR analysis: [references/pr-analysis-template.md](references/pr-analysis-template.md)

## Constraints

### Allowed Commands Only

| Command | Purpose |
|---------|---------|
| `gh auth status` | Check GitHub CLI authentication status |
| `gh repo view` | View repository info |
| `gh pr view` | View PR details |
| `gh pr diff` | View PR changes |
| `gh pr list` | Search PR list |
| `gh search commits` | Search commit messages |
| `gh api` | Query GitHub API (GET only) |
| `git status` | Check repository status |
| `git diff` | Compare changes |
| `git log` | View commit history |
| `base64` | Base64 decoding |
| `python` / `python3` | Data processing (decoding, etc.) |
| `jq` | JSON parsing |

### Denied Patterns

The following commands are **never used**:

| Pattern | Reason |
|---------|--------|
| `gh api --method` / `gh api -X` | No write API calls |
| `gh auth login` | No auth changes |
| `gh repo clone` | No repository cloning |
| `git commit` / `git push` | No code changes |
| `git checkout` / `git switch` | No branch switching |
| `curl` / `wget` | No external HTTP requests |
| `rm` / `mv` / `cp` | No file manipulation |

### Principles

- Do not modify or create code
- Clearly indicate uncertain parts in the analysis result
- Ask the user for additional information if the endpoint cannot be found in the repository
- Do not output sensitive information (API keys, passwords, etc.)
- Guide the user if GitHub CLI authentication is required

## Resources

### references/

| Reference | When to Use |
|-----------|-------------|
| [references/repo-aliases.md](references/repo-aliases.md) | Repository alias mapping — when resolving repo from user input |
| [references/endpoint-card-template.md](references/endpoint-card-template.md) | Endpoint Reference Card output format — for general API analysis output |
| [references/pr-analysis-template.md](references/pr-analysis-template.md) | PR analysis output format — for PR-based API change analysis output |
| [references/framework-detection.md](references/framework-detection.md) | Framework-specific routing/schema detection hints — referenced during Repo Profiling |
