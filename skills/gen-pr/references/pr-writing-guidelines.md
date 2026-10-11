# PR Writing Contract

Apply this contract in full during the title and body writing step in `SKILL.md`.

## Evidence

Use this evidence priority:

1. Actual code diff
2. Commit messages
3. File names and repository structure
4. Code comments

Do not use lower-priority evidence when it conflicts with higher-priority evidence.
Describe observed changes without speculating about intent or effects.
A project template defines format, not evidence that a change or check occurred.

| Speculation | Observed fact |
|---|---|
| Improved the user experience | Added pagination to search results |
| Improved code quality | Removed unused variables and imports |
| Optimized performance | Added an index to the list query |
| Fixed a bug | Handled the 500 response caused by null input |

## Priorities

Apply these priorities in order:

1. Preserve factual accuracy and project-required title and body formats.
2. Cover every significant change without duplication.
3. Keep the title and body concise.

Use 50 title characters and three Key Changes bullets as default targets, not hard limits.
Exceed either target when accuracy, complete coverage, or project requirements need more space.
Do not omit a significant change or combine unrelated facts merely to meet these targets.

## Title

Select exactly one final title internally. Show no candidates or alternatives.
Unless the project requires another format, use one of these formats:

```text
<type>: <subject>
<type>(<scope>): <subject>
```

Use a scope only when one module, package, or product area represents the complete change.

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

Make the subject a concrete description of the core change.
Avoid a trailing period, emoji, or emphasis syntax unless the project requires it.
When the diff contains multiple changes, decide in this order:

1. The most specific change that represents the complete diff
2. The largest observable user or code effect
3. The shortest wording that remains accurate

## Body

When a project template exists, it defines the body structure.
Preserve required Markdown, fixed text, instructions, and checklists.
Fill only placeholders supported by evidence. Leave unverified facts unfilled and checkboxes unchecked.
Retain required unfilled fields rather than inventing answers or deleting required sections.

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

Use one sentence per bullet. Prefer three bullets or fewer under the priorities above.
Add `Technical Details` only when a reviewer needs implementation context to understand or check the change.
Add `Breaking Changes` only for an actual compatibility break. Include specific migration instructions for that break.
Remove empty optional headings and placeholders from the default template.

## Completion Criteria

- There is one selected title and one concise, complete body.
- Every concrete claim about changes or checks has supporting evidence.
- Every significant change is covered without duplication.
- Project-required formats, fixed text, instructions, and unverified checkbox states remain intact.
- No speculation, unsupported filled field, or unnecessary optional section remains.
- Length and bullet targets do not override accuracy, complete coverage, or project requirements.
