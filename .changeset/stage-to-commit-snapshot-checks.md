---
"skills": major
---

Stage to Commit now stops when HEAD or staged content changes during analysis instead of automatically analyzing the new state. Check the changed state and invoke the skill again to commit it.

Success now requires matching commit content, parent, and subject. A mismatch reports the created commit and leaves it in place without automatic rollback. Commit messages describe significant changes without listing every hunk or incidental cleanup.
