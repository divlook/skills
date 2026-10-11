# Skills

A collection of agent skills I use and share.

## Contributor documentation

- Before changing repository files, read [project guidelines](AGENTS.md).
- When changing a skill or its documentation, follow [skill development](docs/skill-development.md).
- Before preparing a pull request or changing release automation, read [pull requests and releases](docs/releasing.md).

## Installation

```bash
npx skills@latest add divlook/skills
```

## Available skills

- [`api-radar`](skills/api-radar): Analyzes and documents REST API endpoints in local projects or GitHub repositories.
  - [When and how to use API Radar](docs/skills/api-radar.md)
- [`gen-pr`](skills/gen-pr): Generates an evidence-backed pull request title and body from a branch diff, and optionally creates the pull request.
  - [When and how to use Gen PR](docs/skills/gen-pr.md)
- [`auto-commit`](skills/auto-commit): Commits current-session changes in logical groups while preserving other sessions' changes and staging.
  - [When and how to use Auto Commit](docs/skills/auto-commit.md)
- [`stage-to-commit`](skills/stage-to-commit): Analyzes the staged snapshot, chooses a Conventional Commit message, and creates one commit.
  - [When and how to use Stage to Commit](docs/skills/stage-to-commit.md)
- [`fix-review-loop`](skills/fix-review-loop): Fixes one code defect through reproduction, repair, execution verification, and explicitly labeled independent review or self-review.
  - [When and how to use Fix Review Loop](docs/skills/fix-review-loop.md)
- [`refine-loop`](skills/refine-loop): Reviews and refines one plan, specification, skill, or document through a PASS/FAIL loop.
  - [When and how to use Refine Loop](docs/skills/refine-loop.md)
