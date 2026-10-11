# Endpoint Reference Card Template

Use the fixed sections below. Apply the evidence levels in [API Radar](../SKILL.md) to each field.
Replace each value placeholder with repository evidence or `Unknown`.
For source-supported absence, use `Source-supported — None` with a citation.
For an inferred value, state the required assumption. For an unknown value, record the missing evidence under `Uncertainties`.

---

## Endpoint Reference Card

### {METHOD} {PATH}

**Revision**: {commitSHA | working tree}

**Purpose**: {Source-supported | Inferred | Unknown} — {purpose or usage scenario}

**Auth**: {Source-supported | Inferred | Unknown} — {authentication method and permission conditions}

**Request**
- Path params: {Source-supported | Inferred | Unknown} — {names, types, requirements, and constraints}
- Query params: {Source-supported | Inferred | Unknown} — {names, types, requirements, and constraints}
- Headers: {Source-supported | Inferred | Unknown} — {names, formats, and requirements}
- Body: {Source-supported | Inferred | Unknown} — {fields, types, requirements, and constraints}

**Response**
- Status: {Source-supported | Inferred | Unknown} — {status}
- Content-Type: {Source-supported | Inferred | Unknown} — {media type}
- Body: {Source-supported | Inferred | Unknown} — {schema or field summary}

**Errors**
- {Source-supported | Inferred | Unknown} — {status, code or message, and trigger condition}

#### Evidence

Cite every purpose, route, permission, schema, response, and error claim.

- Local source: `{relativePath}:L{start}-L{end} ({commitSHA | working tree})`
- GitHub source: `https://github.com/{owner}/{repo}/blob/{commitSHA}/{filePath}#L{start}-L{end}`

#### Uncertainties

- {None, or unresolved item, reason, inspected evidence, and evidence still needed}
