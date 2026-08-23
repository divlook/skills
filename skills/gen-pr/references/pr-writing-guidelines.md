# PR Writing Contract

Apply this contract in full during the title and body writing step in `SKILL.md`.

## Evidence

Use this evidence priority:

1. Actual code diff
2. Commit messages
3. File names and repository structure
4. Code comments

Do not use lower-priority evidence when it conflicts with higher-priority evidence. Describe observed changes without speculating about intent or effects.

| Speculation | Observed fact |
|---|---|
| Improved the user experience | Added pagination to search results |
| Improved code quality | Removed unused variables and imports |
| Optimized performance | Added an index to the list query |
| Fixed a bug | Handled the 500 response caused by null input |

## Title

Commit to exactly one final title and output only that title in one of these formats:

```text
<type>: <subject>
<type>(<scope>): <subject>
```

Use a scope only when the diff clearly identifies one module, package, or product area that represents the complete change.

| Type | Use for |
|---|---|
| `feat` | New functionality |
| `fix` | Defect correction |
| `docs` | Documentation-only changes |
| `style` | Formatting changes with no behavior change |
| `refactor` | Code restructuring that preserves behavior |
| `perf` | Performance changes |
| `test` | Test-only changes |
| `build` | Build system or dependency changes |
| `ci` | CI/CD configuration changes |
| `chore` | Maintenance outside the types above |

Keep the complete title at or below 50 characters. Do not use a trailing period, emoji, or emphasis syntax. Make the subject a concrete description of the core change. Choose internally without showing candidates or alternatives. When the diff contains multiple changes, decide in this order:

1. The most specific change that represents the complete diff
2. The largest observable user or code effect
3. The shortest wording that remains accurate

## Body

When a project template exists, it defines the body structure. Preserve required Markdown, fixed text, instructions, and checklists, and keep generated prose brief. Leave unverified facts and checkboxes unfilled.

Fill only sections supported by evidence:

- Summary: One factual sentence describing the complete change
- Key changes: Up to three one-sentence bullets, only when the summary cannot cover significant changes clearly
- Technical details: Only implementation context a reviewer needs to understand or verify the change
- Breaking changes: Only actual compatibility breaks, with specific migration instructions

Use the fewest sections and bullets that cover every significant change. Each fact appears once.

When no project template exists, start with this minimal template:

```text
## Summary
[One factual sentence describing the complete change]
```

Add `Key Changes` only when one summary sentence cannot clearly cover every significant change:

```text
## Key Changes
- [Significant change]
```

Add `Technical Details` only when a reviewer needs implementation context to understand or verify the change. Add `Breaking Changes` only for an actual compatibility break and include migration instructions. Remove every empty heading and placeholder from the final body.

## Completion Criteria

- There is exactly one selected title, no title alternatives, and one concise, complete body.
- Every concrete sentence has supporting evidence.
- Every significant change is covered with at most three Key Changes bullets.
- The project template's required structure and unverified checkbox states are preserved.
- No speculation, duplication, empty placeholder, or unnecessary section remains.
