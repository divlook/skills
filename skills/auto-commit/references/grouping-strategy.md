# Change Grouping Criteria

The goal is for each commit to describe one change and remain independently revertible.

## Classification Order

Classify each hunk in this order:

1. **Behavior**: Implementation, tests, types, and documentation that complete the same user behavior or bug fix
2. **Required relationship**: Configuration, schemas, and generated artifacts that must be applied together for the project to build or run
3. **Transformation**: Renames, code moves, formatting, and dependency updates with no behavior change
4. **Module**: A single-purpose change within one module when none of the relationships above apply

Do not combine unrelated features merely because they share a type. Do not create groups based only on file count or directory.

## Boundary Check

Apply these questions to each candidate group:

- Can one subject describe every hunk concretely?
- Can this group be reverted without breaking another group?
- Does it include every required test, type, lockfile, and migration?
- If another group is a prerequisite, does the commit order express that dependency?

If any answer is no, split the group again or merge the dependent changes into the same group.

## Mixed Files

When one file contains hunks with multiple purposes, list the file and each purpose in the plan.

- If the hunk boundaries are clear, split them with `git add -p`.
- If the purposes overlap on the same lines or syntax unit, commit the file as one unit.
- If splitting would temporarily break the code, commit the changes together.

## Order

Place prerequisites first so the commit graph applies linearly:

1. Configuration, schemas, and shared refactors directly used by later changes
2. Features or bug fixes
3. Documentation and follow-up cleanup that depend only on that behavior

Prefer actual dependencies over a fixed order by type.

## Completion Criterion

Every hunk belongs to exactly one group, and every group's subject, revert boundary, and prerequisites are explained.
