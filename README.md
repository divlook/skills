> **[한국어](README.ko.md)** | English

# API Radar

An agent skill that analyzes GitHub repository API endpoints **read-only** and documents them as structured **Endpoint Reference Cards**.

Supports path lookup, keyword search, description-based search, and PR/commit/branch analysis.

## Why API Radar?

- **Instant API docs** — Point at any GitHub repo and get structured endpoint documentation in seconds
- **PR review helper** — Quickly understand what API changes a PR introduces before approving
- **Onboarding accelerator** — Quickly find and analyze any API endpoint in an unfamiliar codebase
- **Zero setup** — Works out of the box with any backend stack

## Features

- **Path lookup** — Find endpoints by exact path (e.g. `/v1/users/{user_id}`)
- **Keyword search** — Search by identifier, ViewSet name, or route fragment
- **Description-based search** — Describe the feature in natural language; the skill finds the relevant endpoints
- **PR / commit / branch analysis** — Analyze API changes introduced in a specific PR, commit, or branch
- **Multi-framework support** — Django, DRF, FastAPI, Django Ninja, Express, NestJS, Spring
- **Structured output** — Every result is formatted as an Endpoint Reference Card with auth, request, response, errors, evidence, and a verification curl snippet
- **Read-only** — Never creates, modifies, or deletes any code, file, branch, or PR

## Installation

Install via [npx skills](https://github.com/vercel-labs/skills):

```bash
npx skills add divlook/agent-skill-api-radar
```

> The skill is installed under the name **`api-radar`** (not `agent-skill-api-radar`).  
> After installation your agent will discover it as `api-radar`.

### Install to specific agents

```bash
# OpenCode only
npx skills add divlook/agent-skill-api-radar -a opencode

# Claude Code only
npx skills add divlook/agent-skill-api-radar -a claude-code

# Multiple agents
npx skills add divlook/agent-skill-api-radar -a opencode -a claude-code -a cursor
```

### Global install (available across all projects)

```bash
npx skills add divlook/agent-skill-api-radar -g
```

### Manual install

Copy `skills/api-radar/` into your agent's skills directory:

| Agent | Project path | Global path |
|-------|-------------|-------------|
| OpenCode | `.agents/skills/api-radar/` | `~/.config/opencode/skills/api-radar/` |
| Claude Code | `.claude/skills/api-radar/` | `~/.claude/skills/api-radar/` |
| Cursor | `.agents/skills/api-radar/` | `~/.cursor/skills/api-radar/` |

## Requirements

- [GitHub CLI (`gh`)](https://cli.github.com/) — required for PR/commit/branch analysis
- GitHub CLI authentication (`gh auth login`) — required for private repositories and PR/commit/branch analysis

Standard path/keyword/description searches use the agent's built-in file search tools and do not require `gh`.

## Usage

### Quick Start

```
/api-radar example-owner/example-repo /v1/users
```

Provide a **repository** and a **query**, and the skill returns an Endpoint Reference Card.

### Query types

| Type | Example | When to use |
|------|---------|-------------|
| API path | `example-owner/example-repo /companies/{company_id}/users` | Exact path is known |
| Keyword | `example-owner/example-repo partner` | Part of an endpoint name is known |
| Description | `example-owner/example-repo salary transfer API` | Only the feature is known |
| Repo URL | `https://github.com/example-owner/example-repo /v1/health` | Specifying repo by URL |
| PR number | `example-owner/example-repo PR#123` | Analyze API changes in a PR |
| Commit | `example-owner/example-repo commit 1a2b3c4` | Analyze a specific commit |
| Branch | `example-owner/example-repo branch feature/payments` | Analyze a specific branch |
| Alias | `my-api /v1/health` | Using a configured alias |

### Example output

```
## Endpoint Reference Card

### POST /companies/{company_id}/salary

**Purpose**: Initiates a salary transfer for all employees in the given company.

**Auth**: Bearer JWT — requires permission code `SALARY_TRANSFER` (AccessType.PERMISSION)

**Request**
- Path params: company_id (integer, required)
- Headers: Authorization: Bearer {TOKEN}
- Body: { "transfer_date": "YYYY-MM-DD", "memo": "string" }

**Response (Observed)**
- Status: 200
- Body: { "transfer_id": 1234, "status": "pending" }

**Errors (Observed)**
- 403 PERMISSION_DENIED — missing SALARY_TRANSFER permission
- 400 INVALID_DATE — transfer_date is in the past

#### Evidence
- Routing: https://github.com/owner/repo/blob/abc1234/api/salary/urls.py#L12-L15
- Permission: https://github.com/owner/repo/blob/abc1234/api/salary/views.py#L45
```

## Customization

### Repository Aliases

Edit `references/repo-aliases.md` inside the installed skill to define short aliases for frequently used repositories.

```markdown
| alias     | owner/repo               | note                    |
|-----------|--------------------------|-------------------------|
| my-api    | your-org/your-api        | Django REST Framework   |
| payments  | your-org/payment-service | FastAPI                 |
```

Once configured, use the alias directly in queries:

```
/api-radar my-api /v1/users
/api-radar payments PR#42
```

The file is located at:

| Agent | Project path |
|-------|-------------|
| Claude Code | `.claude/skills/api-radar/references/repo-aliases.md` |
| OpenCode, Cursor | `.agents/skills/api-radar/references/repo-aliases.md` |

## Supported Frameworks

| Language | Framework |
|----------|-----------|
| Python | Django, Django REST Framework (DRF), FastAPI, Django Ninja |
| Node.js | Express, NestJS |
| Java | Spring (RestController) |

Detection is best-effort. If the framework cannot be confirmed, analysis continues with the same output format and uncertain items are noted in the `Uncertainties` section.

## License

MIT — see [LICENSE](LICENSE)
