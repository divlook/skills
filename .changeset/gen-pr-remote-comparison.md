---
"skills": major
---

Gen PR now compares against the remote-tracking target by default. Explicitly request a local comparison draft to compare local branches. Creating a PR from that draft requires analysis against the remote target.

Choose whether to exclude uncommitted changes or approve a scoped commit before drafting. Draft-only runs no longer check source publication. PR creation records both analyzed commit IDs, checks remote source and target readiness, and requests separate approval for target fetches. Commit, push, and existing PR edit approvals remain separate from creation approval.

Project-required formats, factual accuracy, and complete change coverage now take priority over the default 50-character title and three-bullet targets.
