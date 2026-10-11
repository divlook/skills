# skills

## 1.0.0

### Major Changes

- 60f2547: Auto Commit now commits only changes made in the current session by default. To include other sessions' work, explicitly request all staged, unstaged, and untracked changes in the entire working tree.
  
  Preserve excluded changes and their staged state, including separable edits in shared files. Reuse matching staging and split only clearly independent tasks without a separate execution-plan document. Stop on uncertain ownership, detected concurrent changes, or commit failure without automatic fixes or retries. Repository-required checks and hooks still apply.
- 834c43c: Separate user decision waiting from review failure in Fix Review Loop with the new `NEEDS_DECISION` final status. Report `independent review` or `self-review` explicitly, continue decision-independent repairs, validate findings individually, and require execution evidence before completion.
  
  Consumers of the final report must accept `NEEDS_DECISION`, read the new `Review Method` field, and allow absent optional sections. `Iterations` now includes self-review cycles. Code or log tracing alone no longer permits `PASS`; unavailable execution verification produces `BLOCKED`.
- 66543b9: Gen PR now compares against the remote-tracking target by default. Explicitly request a local comparison draft to compare local branches. Creating a PR from that draft requires analysis against the remote target.
  
  Choose whether to exclude uncommitted changes or approve a scoped commit before drafting. Draft-only runs no longer check source publication. PR creation records both analyzed commit IDs, checks remote source and target readiness, and requests separate approval for target fetches. Commit, push, and existing PR edit approvals remain separate from creation approval.
  
  Project-required formats, factual accuracy, and complete change coverage now take priority over the default 50-character title and three-bullet targets.
- 7c17f5f: Refine Loop keeps user decisions pending with `Progress: WAITING` and resumes the same invocation after an answer. Update integrations to distinguish waiting from terminal results and accept omitted, inapplicable report sections. Final reports identify the review method and exact reviewed version. Iterations advance only for changed target content, and connected artifacts no longer have a file-count limit. Update direct resource links from `skills/refine-loop/resources/` to `skills/refine-loop/references/`.
- 2f5004c: Stage to Commit now stops when HEAD or staged content changes during analysis instead of automatically analyzing the new state. Check the changed state and invoke the skill again to commit it.
  
  Success now requires matching commit content, parent, and subject. A mismatch reports the created commit and leaves it in place without automatic rollback. Commit messages describe significant changes without listing every hunk or incidental cleanup.

### Patch Changes

- 01a825e: API Radar now distinguishes branch changes from explicit revision state comparisons and uses the same comparison start for previous endpoint behavior. Committed evidence stays pinned to resolved SHAs, while working-tree analysis identifies its mutable state.
  
  Endpoint cards distinguish source-supported facts, assumptions, missing evidence, and confirmed absence. Change analysis covers PRs, commits, branches, and revision comparisons, with previous and target evidence for modified conditions. Incomplete inventories identify their missing scope as partial analysis.
- e7f5e4d: Keep Fix Review Loop within the reported defect and directly affected paths across review cycles. Check existing implementations for reuse and affected contracts for consistency. Remove only elements introduced unnecessarily or made obsolete by the fix. Require evidence-backed review findings and stop at a valid PASS without seeking optional improvements.

## 0.3.2

### Patch Changes

- 890d635: Clarify API Radar, Auto Commit, Gen PR, Stage to Commit, Fix Review Loop, and Refine Loop guidance. Preserve their existing workflows, safety boundaries, and output formats.
  
  Clarify project, skill development, and release guidance. Add contributor documentation links and distinguish the release policy exception from the status workflow branch filter.

## 0.3.1

### Patch Changes

- eaac46b: Ask how to continue when Gen PR needs commits or pushes, with separately approved preparation and an existing-commits draft option.

## 0.3.0

### Minor Changes

- 7fbedde: Allow models to invoke fix-review-loop, gen-pr, and refine-loop.

## 0.2.0

### Minor Changes

- 886df53: Add pull request, commit, fix review, and artifact refinement skills

## 0.1.0

### Minor Changes

- f63b36a: Automate GitHub Releases with Changesets, standardize pnpm, and document the AI contribution workflow.
