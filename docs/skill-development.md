# Skill Development

Use this workflow for every change to a skill, its references, or its user documentation.

## 1. Map the affected surface

Before editing, inspect each applicable item:

- The target `skills/<name>/SKILL.md`.
- Every file that a pointer from the skill references.
- The matching user documentation under `docs/`.
- The skill entry in `README.md`.
- Neighboring skills, only when you need to identify an established repository convention.

For each renamed or removed file, trace every repository reference to that file.

Finish the map only when you identify or explicitly exclude every affected instruction, pointer, example, and user description.

## 2. Preserve the information hierarchy

Keep instructions that every invocation requires in `SKILL.md`.
For reference material that only specific branches require, use a pointer to the skill's `references/` directory.
Each pointer must name the material.
Each pointer must state the distinct condition that requires the reader to read it.

Keep each rule in one authoritative location.
Prefer repository files and command output over documentation that only copies discoverable state.
Keep each concept's definition, rules, and caveats under one heading.

Use positive, direct instructions.
Use prohibitions only for hard guardrails.
Each workflow step must end with a checkable completion condition.

Finish this step only when every mapped instruction, rule, and pointer follows these placement and writing rules.

## 3. Synchronize user-facing material

Update `docs/skills/<name>.md` when any of these items change:

- Invocation.
- Inputs.
- Outputs.
- Limitations.
- Prerequisites.
- Examples.

Update `README.md` when you add, remove, or rename a skill.
Update `README.md` when the skill's one-line purpose changes.

Finish this step only when every mapped user document and README entry matches the changed skill.

## 4. Check the skill

Exercise the changed path with a representative request or the narrowest available validation command.
Check every relative pointer from the file that contains it.
Check that frontmatter still routes every intended invocation branch.

The change is complete only when all these conditions hold:

- The exercised path produces the documented behavior.
- Every relative pointer resolves from the file that contains it.
- Frontmatter still routes every intended invocation branch.
- Every mapped file matches the changed skill.
