# Repository Aliases

Register repository aliases to use short names instead of `owner/repo` when specifying a repository.

## Alias Rules

- alias: a short string without spaces (e.g. `api`, `web`)
- `owner/repo`: the canonical GitHub notation (e.g. `example-owner/example-repo`)

## Resolution Priority

The `repo` token in the input is resolved in the following order:

1. Starts with `https://github.com/` → URL
2. Contains `/` → `owner/repo`
3. Otherwise → alias (look up in the table below)

## Alias Table

| alias | owner/repo | note |
|-------|------------|------|
| my-api | example-owner/example-repo | (example) |

## Customization

Add your own aliases to the table above to use short names for frequently accessed repositories.

Example:
```
| alias     | owner/repo               | note                    |
|-----------|--------------------------|-------------------------|
| my-api    | your-org/your-api        | Django REST Framework   |
| payments  | your-org/payment-service | FastAPI                 |
```

Once configured, use the alias directly in queries:
```
my-api /v1/users
payments PR#42
```
