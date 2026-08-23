# API Radar

API Radar finds and traces REST API endpoints in a local project or GitHub repository. It reads route definitions, schemas, authentication, permissions, handlers, and error mapping, then returns evidence-backed Endpoint Reference Cards.

## When to use it

Use API Radar when you need to:

- find an endpoint from an exact path, keyword, or behavior description
- understand an endpoint's request, response, authentication, permissions, and errors
- identify REST API changes in a pull request, commit, branch, or comparison
- inventory matching endpoints across a service or monorepo
- inspect a repository without changing its files, branches, or working tree

API Radar is an analysis tool. It reads repository evidence rather than calling the application API or testing a deployed service, and marks unresolved details as unknown. It does not modify source code.

## How to invoke it

Ask for an API analysis in natural language. Include a source when it is not the current project, followed by the endpoint path, keyword, behavior, or revision to inspect.

```text
[optional source] [query]
```

Supported sources:

- current working directory when the source is omitted
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
- evidence level for each field: observed, inferred, or unknown
- source citations for each claim
- uncertainties and the evidence needed to resolve them

Local citations use a relative file path, line range, and commit SHA or `working tree`. GitHub citations are permalinks pinned to a commit SHA.

Pull request analysis starts with PR metadata and a table of added, modified, and removed endpoints, followed by a card for each changed endpoint.
