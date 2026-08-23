# Repository Instructions

## Communication and language

- Reply to each user in the language they use.
- Write repository content in English, including code, comments, documentation, changesets, commit messages, pull request text, and release notes.

## Workflow

1. Research before editing. Inspect the requested target, neighboring conventions, references and callers, tests, documentation, configuration, and automation that can be affected. Account for every affected path and side effect before changing files.
2. Implement the smallest coherent change using the repository's existing structure and terminology. Update every affected reference, test, document, and configuration in the same change.
3. Verify the observable behavior through the narrowest relevant command or scenario. Report exactly what was exercised and any remaining uncertainty.

A task is complete only when the requested behavior works end to end, affected paths are accounted for, and repository content remains internally consistent.

## Context pointers

- **Skill changes:** Before adding, changing, or reviewing a skill, its references, or its user documentation, read [`docs/skill-development.md`](docs/skill-development.md). Follow its inventory and synchronization checks for every affected skill.
- **Pull requests and releases:** Before creating or updating a pull request, selecting a version bump, adding a changeset, or changing release automation, read [`docs/releasing.md`](docs/releasing.md). Every pull request must include a release changeset or an explicit empty changeset, except the automated Changesets release pull request.
