# Endpoint Reference Card Template

Output API analysis results in the following fixed format.

---

## Endpoint Reference Card

### {METHOD} {PATH}

**Purpose**: {1-2 sentences describing the problem this endpoint solves / usage scenario}

**Auth**: {Authentication method (e.g. Bearer JWT) + required permissions/conditions (if any)}

**Request**
- Path params: {if any}
- Query params: {if any}
- Headers: {e.g. Authorization: Bearer {TOKEN}}
- Body: {observed/inferred JSON fields and types, whether required}

**Response (Observed)**
- Status: {e.g. 200}
- Content-Type: {e.g. application/json}
- Body: {observed response example / field summary}

**Errors (Observed)**
- {status_code} {error_code/message} - {trigger condition (observed/inferred basis)}

#### Evidence

All facts included in the output (routing/permissions/schema/errors) should be backed by evidence where possible.

- Permalink format (when possible): `https://github.com/{owner}/{repo}/blob/{commitSHA}/{filePath}#L{start}-L{end}`
- Do not create fixed links using branch names (main/master, etc.)

#### Verification

The values below are placeholders. Do not call internal/production environments directly.

~~~bash
BASE_URL="{BASE_URL}"
TOKEN="{TOKEN}"

curl -sS -i \
  -H "Authorization: Bearer ${TOKEN}" \
  -H "Content-Type: application/json" \
  "${BASE_URL}{PATH}" \
  -d '{"example":"value"}'
~~~

#### Uncertainties

- {uncertain/inferred item} - {why it is uncertain} - {additional files/search terms/tests to verify}
