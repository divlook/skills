# Endpoint Reference Card Template

Use the following fixed sections. Replace every placeholder with repository evidence or `Unknown`.

---

## Endpoint Reference Card

### {METHOD} {PATH}

**Purpose**: {Observed | Inferred | Unknown} — {1-2 sentences describing the problem this endpoint solves or its usage scenario}

**Auth**: {Observed | Inferred | Unknown} — {authentication method and required permissions or conditions}

**Request**
- Path params: {Observed | Inferred | Unknown} — {name, type, requirement, and constraints}
- Query params: {Observed | Inferred | Unknown} — {name, type, requirement, and constraints}
- Headers: {Observed | Inferred | Unknown} — {name, format, and requirement}
- Body: {Observed | Inferred | Unknown} — {fields, types, requirements, and constraints}

**Response**
- Status: {Observed | Inferred | Unknown} — {status}
- Content-Type: {Observed | Inferred | Unknown} — {media type}
- Body: {Observed | Inferred | Unknown} — {schema or field summary}

**Errors**
- {Observed | Inferred | Unknown} — {status, code or message, and trigger condition}

#### Evidence

Cite every purpose, route, permission, schema, response, and error claim.

- Local source: `{relativePath}:L{start}-L{end} ({commitSHA | working tree})`
- GitHub source: `https://github.com/{owner}/{repo}/blob/{commitSHA}/{filePath}#L{start}-L{end}`

#### Uncertainties

- {None, or the unresolved item, why it is uncertain, and the files, search terms, or tests needed to resolve it}
