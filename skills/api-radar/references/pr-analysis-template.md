# PR Analysis Template

PR analysis output follows this fixed order without OpenAPI/spec output.

---

## PR Meta

- PR: #{number} {title}
- Author: @{author}
- Status: {Open | Merged | Closed}
- Branch: {source} -> {target}
- Link: https://github.com/{owner}/{repo}/pull/{number}

## Change Table

| Change | Method | Path | Description |
|--------|--------|------|-------------|
| Added | POST | /... | ... |
| Modified | GET | /... | ... |
| Removed | DELETE | /... | ... |

---

For each changed endpoint, repeat the `## Endpoint Reference Card` template from [endpoint-card-template.md](endpoint-card-template.md).
