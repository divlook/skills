# Skill Development

Use this workflow for every change to a skill, its references, or its user-facing documentation.

## 1. Map the affected surface

Before editing, inspect:

- the target `skills/<name>/SKILL.md`
- every file reached by a pointer from that skill
- the matching user documentation under `docs/`
- the skill entry in `README.md`
- neighboring skills only when needed to identify an established repository convention

Trace renamed or removed files through every repository reference. The map is complete when each affected instruction, pointer, example, and user-facing description is identified or explicitly ruled out.

## 2. Preserve the information hierarchy

Keep instructions required on every invocation in `SKILL.md`. Move branch-specific reference material behind a precise pointer in the skill's `references/` directory. A pointer must name the material and the distinct condition that requires reading it.

Keep each rule in one authoritative location. Prefer repository files and command output over documentation that merely copies discoverable state. Co-locate a concept's definition, rules, and caveats under one heading.

Each workflow step must end with a checkable completion condition. Use positive, direct instructions and reserve prohibitions for hard guardrails.

## 3. Synchronize user-facing material

Update `docs/skills/<name>.md` when invocation, inputs, outputs, limitations, prerequisites, or examples change. Update `README.md` when the skill is added, removed, renamed, or its one-line purpose changes.

Repository content must be written in English even when the user conversation uses another language.

## 4. Verify the skill

Exercise the changed path with a representative request or the narrowest available validation command. Check that every relative pointer resolves from the file containing it and that frontmatter still routes the intended invocation branches.

The change is complete when the exercised path produces the documented behavior and every mapped file is synchronized.
