---
name: api-radar
description: "Maps and documents REST API endpoints from a local project or GitHub repository without changing it. Use for endpoint lookup by path, keyword, or behavior, and for API-change analysis across pull requests, commits, or branches."
---

# API Radar

Trace REST endpoints from route to observable behavior, then produce evidence-backed Endpoint Reference Cards.

Treat the source as immutable. Read files and repository metadata, search code and history, and inspect diffs, commits, branches, and pull requests. Keep the working tree, index, branches, remotes, authentication, and repository files unchanged. For GitHub, use only read operations. Generate analysis in chat; never call the application API.

## Resolve the Input

Accept a source plus a query, or use the current working directory when the source is omitted.

Sources:

- current working directory
- existing relative or absolute local path
- `owner/repo`
- GitHub repository URL

Queries:

- exact API path
- keyword or behavior in any language
- pull request number
- commit
- branch or comparison

Resolve the source in this order:

1. An existing filesystem path is local.
2. A GitHub URL is remote.
3. `owner/repo` is remote only when it is not an existing path.
4. With no explicit source, use the current working directory and treat the full request as the query.

The environment is the source registry. Ask for a source only when neither the request nor the current working directory identifies a searchable project.

Follow [references/source-access.md](references/source-access.md) for the local or GitHub branch. Choose one source path and keep it for the request.

## Workflow

### 1. Establish the Snapshot

Resolve the source, requested revision or comparison, and immutable evidence revision:

- Local working tree: record `working tree` and the current commit SHA when available.
- Local commit or branch: resolve it to a commit SHA.
- GitHub repository or branch: resolve the selected ref to a commit SHA.
- Pull request: record its base, head, metadata, changed files, and diff.

This step is complete when every later file read can be attributed to one source and revision. If a requested ref is ambiguous or absent, ask for that ref rather than silently using the default branch.

### 2. Profile the Project

Find the backend boundary before searching broadly:

1. Inspect top-level structure and dependency or build manifests.
2. Identify services in a monorepo.
3. Find routing, schema, authentication, and exception-handling conventions.

Use [references/framework-detection.md](references/framework-detection.md) for framework-specific routing hints.

This step is complete when likely route-definition locations and the framework convention are identified, or the attempted locations and remaining uncertainty are recorded. An unknown framework does not stop the analysis.

### 3. Find Endpoint Candidates

Search in widening passes:

1. Exact path fragments and route declarations.
2. Original keywords and identifiers.
3. Translated or domain terms when the query is descriptive.
4. Handler, controller, service, schema, test, and generated API-spec references.

For pull requests and comparisons, inspect both directly changed routes and indirect API changes caused by schemas, permissions, serializers, middleware, or error handlers. Trace added and modified endpoints at the target revision; trace removed endpoints at the base revision.

When a broad query produces several plausible endpoints, return a compact candidate table with method, path, purpose, and evidence, then ask which candidate to trace. When the request clearly asks for a complete inventory or change analysis, trace every matching endpoint instead.

This step is complete when every plausible match is either selected for tracing or listed with evidence.

### 4. Trace Observable Behavior

For each selected endpoint, follow actual symbols and composition from the route definition through:

- full path construction, including mounted prefixes and versioning
- HTTP method
- path, query, header, and body inputs
- request schema, validation, and required fields
- authentication and permission conditions
- handler and service behavior needed to explain the endpoint
- response schema, status, and content type
- exception mapping and observed error body

Distinguish evidence levels:

- **Observed**: directly supported by source or diff.
- **Inferred**: follows from composition but is not explicit in the inspected source.
- **Unknown**: evidence was not found after the relevant route, shared middleware, schema, tests, and handlers were checked.

Report framework defaults as unknown unless repository evidence supports them. Omit secrets and user data found in source.

This step is complete when every field in the output is observed, explicitly inferred, or marked unknown with the search evidence.

### 5. Produce the Result

Use:

- [references/endpoint-card-template.md](references/endpoint-card-template.md) for endpoint analysis
- [references/pr-analysis-template.md](references/pr-analysis-template.md) for pull request analysis

For local evidence, cite `relative/path:line-line` and the resolved revision or `working tree`. For GitHub evidence, use a permalink pinned to the commit SHA.

The result is complete when every selected or changed endpoint has a card, every route, permission, schema, response, and error claim has a citation, and every unresolved point appears under `Uncertainties`.
