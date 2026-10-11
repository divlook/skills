# Change Analysis Template

Use this order for PR, commit, branch, and explicit revision comparisons. Exclude OpenAPI and specification output.

## PR Meta

Include this section only for PR analysis:

- PR: #{number} {title}
- Author: @{author}
- Status: {Open | Merged | Closed}
- Branch: {source} -> {target}
- Link: https://github.com/{owner}/{repo}/pull/{number}

## Analysis Scope

- Source: {repository, including the head repository for a fork PR}
- Comparison: {PR or branch changes | commit changes | explicit revision state comparison}
- Start: {startSHA, or empty tree for a root commit}
- Target: {targetSHA}
- Investigated: {services and routing locations}
- Coverage: {Complete within requested scope | Partial analysis — missing scope and reason}

## Change Table

| Change | Method | Path | Description |
|--------|--------|------|-------------|
| Added | POST | /... | ... |
| Modified | GET | /... | ... |
| Removed | DELETE | /... | ... |

For each changed endpoint, use [Endpoint Reference Card Template](endpoint-card-template.md):

- Added: describe the target revision.
- Modified: describe the target revision. Add the section below before `Evidence`.
- Removed: describe the comparison start. Identify the removal with comparison evidence.

### Changed Conditions

Include this section only in modified endpoint cards.
Compare only changed conditions, rather than repeating the complete previous card.
Use the card's evidence levels and citation formats for both sides.

| Condition | Previous value and evidence | Target value and evidence |
|-----------|-----------------------------|---------------------------|
| {input, permission, response, error, or other changed behavior} | {value, evidence level, citation or missing evidence} | {value, evidence level, citation or missing evidence} |

## Uncertainties

Record unresolved coverage and indirect effects here. Keep endpoint-specific uncertainties in their cards.
Use `None` only when no unresolved points remain.
