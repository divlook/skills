# API Radar

API Radar finds and traces REST API endpoints in a local project or GitHub repository. It reads route definitions, schemas, authentication, permissions, handlers, and error mapping. It returns evidence-backed Endpoint Reference Cards.

## When to use it

Use API Radar when you need to:

- find an endpoint from an exact path, keyword, or behavior description
- understand an endpoint's request, response, authentication, permissions, and errors
- identify REST API changes in a pull request, commit, branch, or comparison
- inventory matching endpoints across a service or monorepo
- inspect a repository without changing its files, branches, or working tree

API Radar is an analysis tool. It reads repository evidence rather than calling the application API or testing a deployed service, and marks unresolved details as unknown. It does not change source code.

## How to invoke it

Ask for an API analysis in natural language. Include a source when it is not the current project, followed by the endpoint path, keyword, behavior, or revision to inspect.

```text
[optional source] [query]
```

Supported sources:

- current working directory when you omit the source
- existing relative or absolute local path
- GitHub `owner/repo`
- GitHub repository URL

Supported queries:

- exact API path
- keyword or behavior description in any language
- pull request number
- commit
- branch or comparison

## Examples

### Search the current project

```text
Use API Radar to document /v1/health.
```

```text
Find the file upload API and explain its authentication and error responses.
```

### Search another local project

```text
Use API Radar on ../payments to find the refund endpoints.
```

```text
Analyze /work/services/users branch feature/session-auth for API changes.
```

A local path takes priority over the `owner/repo` form when that path exists. Local analysis does not require GitHub CLI authentication or a GitHub remote.

### Search a GitHub repository

```text
Use API Radar on example-owner/example-repo to document /v1/users/{user_id}.
```

```text
Analyze API changes in https://github.com/example-owner/example-repo PR #123.
```

```text
Find file upload API changes in example-owner/example-repo branch feature/upload.
```

Remote GitHub analysis uses the GitHub CLI (`gh`). If authentication blocks access, run `gh auth login` in your terminal and repeat the request.

### Search by behavior

```text
Use API Radar to find every endpoint that lets an administrator suspend a user.
```

For a broad request with several plausible matches, API Radar returns a candidate table and asks which endpoint to trace. Ask for "every matching endpoint" or an "inventory" when all matches should be analyzed.

## What the result contains

Each Endpoint Reference Card includes:

- HTTP method and fully composed path
- endpoint purpose
- authentication and permission conditions
- path, query, header, and body inputs
- response status, content type, and schema
- error responses and their trigger conditions
- evidence level for each field: source-supported, inferred, or unknown
- source citations for each claim
- uncertainties and the evidence needed to resolve them

Local citations use a relative file path, line range, and commit SHA or `working tree`.
GitHub citations are permalinks pinned to a commit SHA. Each card identifies its analysis revision.

## Evidence levels

- **Source-supported**: source or diff supports the claim, including explicit route composition. Citations identify the necessary connections.
- **Inferred**: the claim depends on an execution, environment, or external-system assumption. The result states that assumption.
- **Unknown**: inspected evidence does not establish the value. The result identifies inspected evidence and what remains missing.

These levels describe repository evidence, not successful live requests.
`Source-supported — None` means evidence establishes absence. For example, an explicit request schema can establish that the endpoint accepts no body.
A missing schema means `Unknown`, not `None`. Framework defaults remain unknown unless repository evidence supports them.

## Analysis revisions and coverage

Committed evidence uses resolved commit SHAs rather than moving branch names.
Search results locate candidates. API Radar confirms them at the analysis revision and inspects routing locations there for additional matches.
Working-tree analysis includes uncommitted files. It labels that evidence as `working tree` rather than an immutable snapshot.
If API Radar detects a file change during analysis, it reads the affected evidence again.

The result identifies investigated services, routing locations, and revisions.
It labels incomplete coverage as `Partial analysis` and identifies the missing scope.
Dynamic routing and unresolved effects from shared middleware can prevent a complete inventory.
A request for every matching endpoint keeps its requested scope. A partial result does not claim to be a complete inventory.

## Change analysis

| Request | Comparison start | Target |
|---------|------------------|--------|
| PR or branch changes | Common ancestor of base and head | Head commit |
| Commit changes | Parent commit | Requested commit |
| Explicit revision state comparison | Requested start commit | Requested target commit |

The same comparison start determines the change list and previous endpoint behavior.
For branch changes, specify the base when the repository does not establish one.
For merge commits, specify the parent when more than one could apply. A root commit compares against the empty tree.

Change analysis identifies the comparison meaning, start, target, and investigated scope.
PR analysis also includes PR metadata. Commit, branch, and state comparisons do not require it.
Each analysis includes a table of added, modified, and removed endpoints, followed by endpoint cards:

- Added: the target state.
- Modified: the target state and a table of changed conditions with previous and target evidence.
- Removed: the previous state at the comparison start, with evidence of removal.

Modified cards compare only changed inputs, permissions, responses, errors, or other behavior. They do not repeat the entire previous card.

```text
Analyze API changes on ../payments branch feature/refunds relative to main.
Compare the API states at main and feature/refunds in ../payments.
```

The first request examines branch changes from the common ancestor. The second compares the two revision states directly.
