# Change Grouping Criteria

Use this reference when task boundaries or dependencies are unclear. Group only changes within the authorized scope.

## Boundaries

- Keep implementation, tests, types, configuration, and documentation for one task together.
- Keep required schemas, lockfiles, migrations, and generated artifacts with the changes that require them.
- Separate clearly independent tasks. A shared directory or commit type does not make tasks related.
- Prefer one coherent group when finer separation needs speculative analysis.
- Keep overlapping changes from authorized tasks together when separation would break the result.
- If a group requires excluded uncommitted changes, stop rather than expand the scope.

For an ambiguous boundary, check whether one concrete subject describes the main change and whether the groups can be reverted independently.
Merge dependent changes when separate commits would be invalid. Do not optimize every possible split.

## Order

Place actual prerequisites before their dependents. Do not impose a fixed order by commit type.
Documentation and tests that complete a task belong with that task, not in automatic follow-up commits.

## Completion Criterion

Every authorized change belongs to one coherent group. Each group has a concrete subject and no missing uncommitted prerequisite.
