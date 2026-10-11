# PR Creation Procedure

Read this procedure for `--create`, `create-pr`, or a missing remote comparison ref in create mode.
Draft-only runs do not use this procedure.

## 1. Resolve the remote target

Use `refs/remotes/origin/<target>` as the comparison ref for creation.
If the draft used a local comparison, explain the switch to the remote target.

Query the remote target without changing local refs:

```bash
git ls-remote --heads origin "refs/heads/<target>"
```

If the query fails, report freshness as unknown and return the command error. Do not create or edit a PR.
If the target is absent remotely, identify the missing branch and ask for the target again.
Compare the returned commit ID with the local remote-tracking ref's commit ID:

```bash
git rev-parse --verify --end-of-options "refs/remotes/origin/<target>^{commit}"
```

When the ref is missing or the IDs differ, request approval to fetch that target and update that ref only.
Show the exact ref update, including any rewind. After approval, run:

```bash
git fetch --no-tags --no-write-fetch-head --recurse-submodules=no origin "+refs/heads/<target>:refs/remotes/origin/<target>"
```

On refusal, retain any existing draft and stop PR creation. Do not fetch automatically.
After a successful fetch, repeat the target query and local ID comparison.
If they still differ, report the changed remote state and request a new refresh decision.
Reuse an inclusion choice only while the inspected uncommitted changes remain unchanged.
When reached during initial branch resolution, finish the target checks and return to Step 1 in the [skill workflow](../SKILL.md).
If an analysis snapshot exists, repeat Steps 2, 4, and 5 after changing the comparison ref or its commit ID.
Complete this section when the remote target exists and its commit matches the local comparison ref.

## 2. Check the source and analysis snapshot

Repeat Step 2 for uncommitted source changes. Honor any choice that still applies to the inspected changes.
Resolve the current source and comparison commit IDs with the Step 4 commands.
Compare both IDs with the analysis snapshot.
If either ID changed, repeat Steps 4 and 5, even when the complete diff is unchanged.
This includes empty commits and commit-message changes.

Query the remote source:

```bash
git ls-remote --heads origin "refs/heads/<source>"
```

On query failure, report publication status as unknown and return the error. Do not assume the source is published.
A missing remote source or a different commit ID requires a publishing decision.
Offer approval for a specific source push, keeping the draft without creation, or stopping.
A differing ID alone does not establish that the remote branch is behind the local branch.
Do not infer force-push approval from creation approval.

Perform only an explicitly approved push under the repository's existing workflow.
After preparation, repeat the target and source checks and the snapshot comparison.
If preparation fails, report the actual error or missing capability.
Ask the user to complete the approved work before resuming.

Complete this section when the remote source equals `source_commit` and the remote target equals `comparison_commit`.
The draft must satisfy the writing contract for those recorded commits.

## 3. Create or edit one PR

List open PRs with the same source and target:

```bash
gh pr list --head "<source>" --base "<target>" --state open --json url,title,body,baseRefName,headRefName
```

If lookup fails, return its error instead of guessing whether a PR exists.
If one PR exists, show its URL and the generated draft. Ask for separate approval to update it.
If multiple PRs exist, ask the user to select one and approve its update.
Selection alone does not authorize editing.

After any approval wait, repeat Sections 1 and 2 before writing.
If the draft changes after update approval, show the revised draft and renew approval for that edit.
If lookup returned an empty array, create the PR and retain the returned URL:

```bash
gh pr create --base "<target>" --head "<source>" --title "<title>" --body "<body>"
```

For an approved existing PR update, edit the selected URL explicitly:

```bash
gh pr edit "<PR URL>" --title "<title>" --body "<body>"
```

Complete this section when one authorized write succeeds and its PR URL is retained.

## 4. Check the retained PR

Read the retained URL after creating or editing:

```bash
gh pr view "<PR URL>" --json url,title,body,baseRefName,headRefName,baseRefOid,headRefOid
```

Check all these conditions:

- The URL is present and identifies the retained PR.
- The title, body, baseRefName, and headRefName exactly match the generated and resolved values.
- The headRefOid equals `source_commit`.
- The baseRefOid equals `comparison_commit`.

On success, output only the URL and title.
Complete delivery only when every check passes.
On command failure or a mismatch, report the error or difference without claiming success.
Include the generated title and body and the correctly escaped command for manual execution.
Manual instructions do not authorize another write or bypass missing preparation approval.
