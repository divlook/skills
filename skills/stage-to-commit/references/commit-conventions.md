# Commit Message Conventions

Apply every rule below when choosing a message for `stage-to-commit`.

## Message contract

Describe the staged snapshot's observable changes. Ground every title, body line, issue reference, and breaking-change claim in the diff.
Infer nothing from unstaged files or intent.

Use:

```text
type(scope): subject

body

footer
```

The title is required. Scope, body, and footer are conditional.

## Type

Choose the type that represents the snapshot's dominant change:

| Type | Use for |
|---|---|
| `feat` | New user-visible capability |
| `fix` | Corrected faulty behavior |
| `docs` | Documentation only |
| `style` | Formatting or whitespace with no behavior change |
| `refactor` | Restructuring with unchanged behavior |
| `perf` | Improved runtime or resource use |
| `test` | Tests only |
| `build` | Build system or dependency changes |
| `ci` | Continuous integration or delivery configuration |
| `chore` | Maintenance outside source and tests |
| `revert` | Reversal of an earlier commit |

For a mixed snapshot, use the most significant type and account for meaningful
secondary changes in the body.

## Scope

Add a short scope only when it identifies a meaningful subsystem or component.
Follow scopes in the recent commit log. Omit it for repository-wide changes or
when it merely repeats the subject.

## Subject

- State the concrete change, not a vague benefit or intent.
- Match the recent commit log's Korean or English style.
- Keep the type lowercase.
- Use imperative mood for English.
- End without a period.
- Target 50 characters. Never exceed 72 characters.

Examples:

| Vague | Concrete |
|---|---|
| `fix: improve logic` | `fix(api): handle empty responses` |
| `refactor: improve readability` | `refactor(user): consolidate duplicate validation` |
| `perf: optimize performance` | `perf(query): remove duplicate API calls` |

## Body

Add a body only when the title cannot represent every significant staged
change or a necessary reason is visible in the diff.

- Separate it from the title with a blank line.
- Use bullets for multiple independent changes.
- Describe what changed and, only when evidenced, why.
- Wrap prose at 72 characters.

Example:

```text
feat(cart): add cart quantity limit

- Limit the maximum quantity to 99
- Add an error message for excessive input
- Clamp existing excessive quantities to 99
```

## Footer

Include a footer only when the staged diff provides the required evidence.

Issue references:

```text
Closes #123
Refs #456
```

For an incompatible public contract change, mark the title with `!` and add a
`BREAKING CHANGE:` footer that states what consumers must update:

```text
feat(api)!: rename response collection

BREAKING CHANGE: Clients must use data.results instead of data.items
```

Separate the footer from the body with a blank line.
