# Repository Instructions

## Communication and language

- Reply to each user in the language they use.
- Write all repository content in English. This includes code, comments, documentation, changesets, commit messages, pull request text, and release notes.

## Workflow

1. Research before editing. Inspect the target and all potentially affected conventions, references, callers, tests, documentation, configuration, and automation. Finish research only when you can identify every affected path and side effect.
2. Implement the smallest coherent change with the repository's existing structure and terminology. Finish implementation only when the same change updates every affected reference, test, document, and configuration.
3. Check observable behavior with the narrowest relevant command or scenario. Report the exact checks you exercised. Report any remaining uncertainty.

A task is complete only when all these conditions hold:

- The requested behavior works end to end.
- You can account for every affected path.
- Repository content remains internally consistent.

## Context pointers

- **Skills:** Read [the development workflow](docs/skill-development.md) before you add, change, or review skills, their references, or their user documentation. Apply its inventory and synchronization checks to every affected skill.
- **Releases:** Read [the release policy](docs/releasing.md) before any of these actions:
  - Create or update a pull request.
  - Select a version bump.
  - Add a changeset.
  - Change release automation.
