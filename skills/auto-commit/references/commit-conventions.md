# Commit Message Conventions

Use the format `type(scope): English subject`. Omit the scope when it adds no meaning.

## Type

| Type | Use for |
|---|---|
| `feat` | New user behavior |
| `fix` | Correcting incorrect behavior |
| `refactor` | Structural changes without behavior changes |
| `perf` | Performance changes |
| `test` | Test-only changes |
| `docs` | Documentation-only changes |
| `style` | Formatting changes without behavior changes |
| `build` | Build tooling or dependencies |
| `ci` | Continuous integration configuration |
| `chore` | Maintenance not covered by another type |

Use the type that represents the group's primary behavior. Multiple types do not require further splitting.

## Subject

- State the changed behavior or structure concretely.
- Write in English without a trailing period.
- Aim for 50 characters and never exceed 72 characters.
- Name the target instead of ending with a vague word such as `improve`, `cleanup`, or `update`.

Examples:

```text
fix(api): prevent null dereference on empty responses
refactor(auth): unify token validation paths
build: update dependencies for TypeScript 6
```

## Body

Use a body only when a significant change or compatibility requirement needs more explanation.

- Separate it from the subject with a blank line.
- Use bullets for multiple changes.
- Explain what changed and why the changes belong together. Do not repeat the implementation process.
- Wrap lines at 72 characters.

For an incompatible change, add a final paragraph beginning with `BREAKING CHANGE:` and state what users must change. Add `Closes #123` or `Refs #123` as a footer only when you verify the issue relationship.

## Completion Criterion

Each message states the group's main change accurately. It does not imply changes from another group or describe every hunk.
