# Pull Requests and Releases

This repository uses Changesets to record release intent, prepare version pull requests, and create GitHub Releases. It is a private Changesets package: nothing is published to npm.

## Pull request changesets

Before creating or updating a pull request, classify its release impact and commit exactly one of these outcomes:

- `major`: an incompatible change that requires users to change existing usage
- `minor`: a backward-compatible capability or skill addition
- `patch`: a backward-compatible fix, documentation correction that changes usage guidance, or other user-visible improvement
- empty changeset: internal maintenance with no user-visible release impact

Create a release changeset with:

```bash
pnpm changeset
```

Write the summary in English as a concise, user-facing statement. Describe the observable result rather than implementation details.

For a pull request that should not produce a release, create and commit an empty changeset:

```bash
pnpm changeset --empty
```

The automated `Release Skills` pull request is the only exception because it consumes existing changesets instead of adding one. The pull request status workflow reports whether every other pull request includes release intent.

## Automated release flow

1. A pull request with a changeset is merged into `main`.
2. `.github/workflows/release.yml` creates or updates the `Release Skills` pull request. Changesets consumes pending files, updates `package.json` and `CHANGELOG.md`, and removes the consumed changesets.
3. Merging `Release Skills` into `main` runs the workflow again.
4. `pnpm release` creates the `skills@<version>` tag. The Changesets action creates the matching GitHub Release from the generated changelog.

Several merged pull requests may accumulate in one release pull request. Their bump levels and summaries are combined by Changesets.

## Repository setup

The release workflow requires this GitHub repository setting:

- **Settings → Actions → General → Workflow permissions → Allow GitHub Actions to create and approve pull requests**

The workflow uses the repository-provided `GITHUB_TOKEN`; no npm token is required. Its `contents: write` permission creates commits, tags, and GitHub Releases, while `pull-requests: write` maintains the release pull request.

## Release automation changes

When changing the Changesets configuration or workflows, verify all of these paths:

- a normal pull request reports its changeset status
- a push to `main` with pending changesets selects version mode
- merging the release pull request selects publish mode
- private package versioning and tagging remain enabled in `.changeset/config.json`
- no npm registry credentials or publish step are introduced

A release automation change is complete when the configuration parses, the local Changesets status command succeeds for the intended state, and the workflow permissions cover only checkout, release pull requests, tags, and GitHub Releases.
