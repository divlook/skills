---
"skills": major
---

Separate user decision waiting from review failure in Fix Review Loop with the new `NEEDS_DECISION` final status. Report `independent review` or `self-review` explicitly, continue decision-independent repairs, validate findings individually, and require execution evidence before completion.

Consumers of the final report must accept `NEEDS_DECISION`, read the new `Review Method` field, and allow absent optional sections. `Iterations` now includes self-review cycles. Code or log tracing alone no longer permits `PASS`; unavailable execution verification produces `BLOCKED`.
