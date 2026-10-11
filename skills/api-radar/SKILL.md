---
name: api-radar
description: "Maps and documents REST API endpoints from a local project or GitHub repository without changing it. Use for endpoint lookup by path, keyword, or behavior, and for API-change analysis across pull requests, commits, or branches."
---

# API Radar

Trace REST endpoints from each route to its observable behavior. Produce evidence-backed Endpoint Reference Cards.

Read files and repository metadata. Search code and history. Inspect diffs, commits, branches, and pull requests.

Keep the working tree, index, branches, remotes, authentication, and repository files unchanged. For GitHub, use only read operations. Generate analysis in chat. Never call the application API.

## Resolve the Input

Accept a source plus a query. If the request omits the source, use the current working directory.

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

Ask for a source only when neither the request nor the current working directory identifies a searchable project.

Read [Source Access](references/source-access.md) after you resolve a local or GitHub source. Use its matching access branch.
Use the same source repository throughout the analysis.

Complete input resolution only when you have a searchable source, query, and matching access branch.

## Workflow

### 1. Establish the Analysis Revisions

Resolve the requested revision or comparison:

- Working tree: include uncommitted files. Record `working tree` and the current commit SHA when available.
- Commit or branch: resolve the selected ref to a commit SHA.
- GitHub repository without a requested ref: resolve its default branch to a commit SHA.
- Change analysis: establish the comparison start and target under [Source Access](references/source-access.md).

Read committed evidence at the resolved SHAs. Use search results only to discover candidates. Confirm each candidate at the analysis revision.
The working tree is mutable, not an immutable snapshot. If you detect a file change during analysis, read the affected evidence again.
Label its citations as `working tree`, not as committed content.

If a requested ref is ambiguous or absent, ask for that ref. Keep the requested revision instead of substituting the default branch.
Complete this step when each file read has an identified analysis revision or the `working tree` label.
For changes, also record both comparison SHAs and the comparison meaning.

### 2. Profile the Project

Find the backend boundary before searching broadly:

1. Inspect top-level structure and dependency or build manifests.
2. Identify services in a monorepo.
3. Find routing, schema, authentication, and exception-handling conventions.

After identifying a supported framework, read its routing hints in [Framework Routing Hints](references/framework-detection.md).

Record the services, route-definition locations, and framework conventions you inspect. Record unresolved routing boundaries under `Uncertainties`.
An unknown framework does not stop the analysis.
Complete this step when the investigated scope and unresolved boundaries are explicit.

### 3. Find Endpoint Candidates

Search in widening passes:

1. Exact path fragments and route declarations.
2. Original keywords and identifiers.
3. Translated or domain terms when the query is descriptive.
4. Handler, controller, service, schema, test, and generated API-spec references.

For changes, inspect directly changed routes. Inspect indirect API changes through schemas, permissions, serializers, middleware, and error handlers.
Trace added and modified endpoints at the comparison target. Trace removed endpoints at the comparison start.
Use the same comparison start for the change list and previous behavior.

When a broad query produces several plausible endpoints, return a compact candidate table. Include method, path, purpose, and evidence.
Ask which candidate to trace. For an inventory or change analysis, trace every matching endpoint in the requested scope instead.

Complete this step when you select, list for user selection, or exclude each discovered candidate with a reason.
Record uninvestigated services, dynamic routing, and unresolved indirect effects under `Uncertainties`.
Keep the requested scope. Label incomplete coverage as partial analysis rather than presenting a subset as a complete inventory.

### 4. Trace Observable Behavior

For each selected endpoint, follow actual symbols and composition from the route definition through:

- full path construction, including mounted prefixes and versioning
- HTTP method
- path, query, header, and body inputs
- request schema, validation, and required fields
- authentication and permission conditions
- handler and service behavior needed to explain the endpoint
- response schema, status, and content type
- exception mapping and error body

Distinguish evidence levels:

- **Source-supported**: explicit source or diff supports the claim. This includes composition with traceable source connections, such as mounted paths.
- **Inferred**: the claim requires an assumption about execution, the environment, or an external system. State the assumption.
- **Unknown**: the inspected evidence does not establish the value. Record the searches and relevant files inspected.

Cite each source connection needed for a composed claim. These levels describe repository evidence, not results from live requests.
Use `Source-supported — None` only when evidence establishes absence. Missing evidence means `Unknown`, not `None`.
Report framework defaults as unknown unless repository evidence supports them. Omit secrets and user data found in source.

Complete this step when every output field has an evidence level, supporting citations, or an explanation of missing evidence.

### 5. Produce the Result

Use:

- [Endpoint Reference Card Template](references/endpoint-card-template.md) for endpoint analysis
- [Change Analysis Template](references/pr-analysis-template.md) for pull request, commit, branch, or revision comparisons

Apply the citation formats in [Endpoint Reference Card Template](references/endpoint-card-template.md) to every evidence claim.
Identify the investigated services, routing locations, and analysis revisions before the cards.

Complete the result only when all these conditions apply:

- Every selected or changed endpoint has a card.
- Every route, permission, schema, response, and error claim has a citation.
- Every unresolved point appears under `Uncertainties`.
- Change analysis shows previous and target evidence for each changed condition.
- You label incomplete coverage as `Partial analysis` with the missing scope.
