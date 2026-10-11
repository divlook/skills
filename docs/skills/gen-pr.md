# Gen PR

Gen PR generates one pull request title and body from committed branch changes.
It returns a draft or creates the pull request after explicit authorization.

## When to use it

Use Gen PR when you need to:

- draft a pull request from a branch diff
- preserve and fill a repository pull request template
- create a new pull request from the generated draft
- update an existing pull request after separate approval

## How to invoke it

```text
/gen-pr [source > target] [--create]
```

The source defaults to the current branch. The target defaults to the remote default branch.
Add the independent `--create` token to authorize PR creation, not preparation or an existing PR update.
Otherwise, Gen PR returns a draft with `edit-draft`, `create-pr`, and `stop` as next actions.

Examples:

```text
/gen-pr
/gen-pr feature/search > main
/gen-pr feature/search > main --create
```

To compare local branches instead, explicitly request a local comparison draft:

```text
/gen-pr Compare local feature/search against local main and return a draft.
```

## Comparison and analysis

By default, `main` means the GitHub target branch, with `refs/remotes/origin/main` as the comparison ref.
Gen PR does not substitute local `main`, even if it exists.
An explicit local comparison request uses the local ref and produces a labeled local comparison draft.
Creating a PR from that draft requires another analysis against the remote target.

Draft mode reads existing local refs without querying the remote or checking publication status.
An unpublished source branch can produce a draft.
The draft states that the remote-tracking target's freshness was not checked.
If that ref is missing, Gen PR asks for another target or for the ref to be prepared before resuming.
It does not fetch automatically or fall back to the local target.

Gen PR records the source and comparison commit IDs before reading commits and the complete three-dot diff.
All analysis commands use those fixed IDs.
The draft describes every significant change without treating commit messages as stronger evidence than the diff.

## Uncommitted changes

When the source is the current branch, Gen PR checks staged, unstaged, and untracked changes before drafting.
If any exist, it asks how to continue:

- **Use existing commits:** Draft from committed changes and report the exclusion of uncommitted changes.
  This option is available only when the committed diff is nonempty.
- **Commit selected changes and include them:** Identify the intended changes and approve the exact commit scope before execution.
  This does not authorize every uncommitted change or a push.
- **Stop:** Finish without committing or drafting.

Approved commits follow the repository's existing workflow. Gen PR adds no commit grouping or message rules.
It analyzes the resulting commits, not uncommitted changes mentioned only in the body.
After a scoped commit, unchanged, inspected changes outside that scope remain excluded without another inclusion question.
It asks again if remaining inspected changes change or new uncommitted changes appear.
With no uncommitted source changes, it proceeds without a preparation question.
Changes on another checked-out branch are outside the requested source.
An empty committed diff with no uncommitted source changes returns `No changes` and the compared refs.

For example, with one committed feature and an unstaged correction, Gen PR asks before drafting.
You can exclude the correction or approve a scoped commit that includes it.
If every intended change is uncommitted, the existing-commits option is unavailable.

## Title and body

Gen PR selects one evidence-backed title and concise body.
It uses a Conventional Commit-style title unless the project requires another format.
It preserves the project template's required structure, fixed text, instructions, and unchecked unverified items.
Without a template, it starts with a factual Summary and adds sections only when needed.

The 50-character title and three Key Changes bullets are default targets, not hard limits.
Project-required formats, factual accuracy, and complete coverage take priority.
Gen PR can use more space rather than omit changes or combine unrelated facts.

## PR creation and preparation approval

Creation requires the GitHub CLI (`gh`) to be installed and authenticated.
It uses the remote target comparison, including when invoked with an explicit local comparison request.
Before an empty-diff decision in create mode, Gen PR queries the remote target and checks the local remote-tracking ref.

If the target ref is missing or stale, Gen PR requests approval for a fetch of that target only.
It shows the ref update, including any rewind, and rechecks the result after fetching.
Refusing the fetch keeps any existing draft but stops PR creation.
Approved refreshes require another analysis and title/body check when a snapshot already exists.
Gen PR does not pull or check out branches.

Before writing a PR, the remote source must match the analyzed source commit.
The remote target must match the analyzed comparison commit.
A missing or different remote source prompts a separate publishing decision.
A difference does not automatically authorize a force push.
A remote query failure reports unknown readiness and prevents PR creation or editing.

If either analyzed commit ID changes, Gen PR analyzes again, even when the diff stays identical.
`--create` and `create-pr` authorize one PR creation only.
Commits, pushes, target fetches, and existing PR edits require their own explicit approvals.
Gen PR performs only approved preparation and reports actual preparation failures before asking you to complete that work.

An existing open PR for the same source and target requires separate update approval.
After creation or editing, Gen PR checks the retained URL, title, body, branch names, and both commit IDs.
It reports success only when they match the analyzed draft and snapshot.
These checks observe the remote at specific times. They cannot lock branches against later concurrent updates.
