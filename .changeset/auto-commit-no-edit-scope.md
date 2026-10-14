---
"skills": minor
---

Auto Commit now defaults to the entire working tree in sessions that have made no edits, without requesting another approval. After editing, it still defaults to changes identified from the current session's history. Explicit scopes take precedence: an unqualified request to commit all changes includes the entire working tree, while a session-qualified request stays session-only. Missing or incomplete edit history stops default-scope commits rather than widening their scope. To keep the previous session-only behavior in a no-edit session, explicitly request changes made in the current session.
