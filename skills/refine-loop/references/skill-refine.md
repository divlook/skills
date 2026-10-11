# Skill Review

Use this resource for skill packages: frontmatter, invocation, context pointers, workflow, tool constraints, resources, and output behavior.

## Invocation and Packaging

Check that:

- The lowercase hyphenated `name` matches the folder.
- Frontmatter follows the host's skill format.
- Invocation is intentional. Manual-only skills declare that mode. Model-invoked skills have a narrow model-facing description.
- The description is concise and carries each genuine trigger branch without synonyms or body detail.
- When-to-use boundaries are understandable without repeating the trigger throughout the body.
- Loading, naming, and permission assumptions match the host environment.

## Workflow

Check that:

- Steps appear in execution order and each ends on a checkable, demanding completion criterion.
- Ambiguous input, loops, delegation, failures, and stop conditions have explicit handling when relevant.
- User decisions remain user-owned unless the invoking request explicitly delegates them.
- Delegation activation is exact, request-local, and unable to bypass destructive, external, credential, deployment, or conflicting-change boundaries.
- Delegated decisions are visible in the final output.
- The output contract makes completion distinguishable from partial progress.

## Information Hierarchy

Check that:

- Every branch uses the main workflow. Branch-only reference sits behind a clear pointer.
- Resources reduce main-file complexity or isolate genuinely case-specific rules.
- Each concept's definition, rules, and caveats are co-located.
- One authoritative location owns each behavior.
- Environment lookups remain in the environment unless caching them prevents a real recurring failure.
- Every line changes agent behavior. Stale exposition and default-behavior reminders are absent.
- Positive target behavior leads, with prohibitions reserved for hard guardrails.

## Portability

Check that:

- Workflow steps describe capabilities rather than one tool, agent, model, or command.
- Environment-specific setup stays out of portable artifacts.
- Tool examples or mappings appear only when the host distinction materially changes execution.
- Instructions agree with repository and global agent guidance.

`PASS` requires a correctly invoked agent to follow the same process on repeated runs without guessing hidden assumptions.
The agent must read only the reference needed for its branch.
