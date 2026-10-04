# Pull Requests and Releases

Changesets records release intent, prepares version pull requests, and creates GitHub Releases.
The package is private. The release workflow does not publish it to npm.

## Pull request changesets

Before creating or updating a pull request, classify its release impact.
Commit exactly one of these outcomes:

- `major`: an incompatible change that requires users to change existing usage
- `minor`: a backward-compatible capability or skill addition
- `patch`: a backward-compatible fix, documentation correction that changes usage guidance, or other user-visible improvement
- empty changeset: internal maintenance with no user-visible release impact

For a release, create a changeset:

```bash
pnpm changeset
```

Write a concise, user-facing summary in English. Describe the observable result, not implementation details.

For a pull request with no release impact, create an empty changeset:

```bash
pnpm changeset --empty
```

The automated `Release Skills` pull request is the only policy exception.
It consumes existing changesets instead of adding one.
The pull request is ready when its committed changeset records the intended release impact and summary, or explicitly records no impact.

### Status workflow

`.github/workflows/changeset-status.yml` reports release intent through a pull request comment.
It runs on `pull_request_target`.
Its inspection job uses `contents: read`. Its comment job uses `pull-requests: write`.

The workflow skips any source branch that starts with `changeset-release/`.
This branch filter is broader than the policy exception for the automated release pull request.
A skipped status job does not exempt another pull request from the changeset policy.

## Automated release flow

`.github/workflows/release.yml` runs after a push to `main`:

1. A contributor merges a pull request with a release changeset into `main`.
2. With pending release changesets, the workflow creates or updates the `Release Skills` pull request.
   Changesets updates `package.json` and `CHANGELOG.md`. It removes the consumed changesets.
3. A contributor merges `Release Skills` into `main`. The workflow runs again.
4. With no pending release changesets, the action runs the release script from `package.json`.
   The script creates the `skills@<version>` tag. The action creates the matching GitHub Release from the generated changelog.

Several merged pull requests may accumulate in one release pull request.
Changesets combines their bump levels and summaries.
An empty changeset records no release impact. It does not request a version bump.

### Permissions and private package

Enable this repository setting:

- **Settings → Actions → General → Workflow permissions → Allow GitHub Actions to create and approve pull requests**

The release job grants `contents: write` for commits, tags, and GitHub Releases.
It grants `pull-requests: write` for the release pull request.
The action uses the repository-provided `GITHUB_TOKEN`. The workflow needs no npm token.

The private package contract spans `package.json` and `.changeset/config.json`.
Keep the package private. Keep private package versioning and tagging enabled.
The publish-mode script tags this private package without publishing it to npm.

## Release automation changes

Read `package.json`, `.changeset/config.json`, and both workflows before changing release automation.
Use those files as the authority for scripts, settings, triggers, and permissions.

Check all these paths when changing release automation:

- A normal pull request reports its changeset status.
- A source branch with the `changeset-release/` prefix skips status inspection.
- A push to `main` with pending release changesets selects version mode.
- A push to `main` without pending release changesets selects publish mode.
- Private package versioning and tagging remain enabled.
- The workflows use only the permissions described above.
- The workflows contain no npm credentials or npm publication step.

Run `pnpm changeset:status` to check local release intent without changing versions or creating tags.
An automation change is complete only when all these conditions hold:

- Configuration parses.
- The local status command succeeds for the intended state.
- Workflow permissions cover only checkout, release pull requests, tags, and GitHub Releases.

Report workflow paths that local checks cannot exercise.
Local checks do not prove GitHub permissions, pull request comments, tags, or GitHub Releases work at runtime.
