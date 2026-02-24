# Framework Detection

A reference document used during the Repo Profiling step to identify backend framework/routing/schema hints on a best-effort basis.

## Quick Structure Hints (Monorepo / Layout)

Check the following directory/file hints first:

| Hint | Meaning |
|------|---------|
| `apps/`, `services/`, `packages/` | Monorepo / service separation |
| `src/`, `api/`, `controllers/`, `routes/` | General backend structure |
| `openapi`, `swagger`, `schema`, `dto`, `serializer`, `model` keywords | Schema/model definitions |

## Framework-Specific Routing / Schema Hints

Where possible, find "routing definitions" first, then trace back to request/response schemas (Serializer / Schema / DTO / Request / Response).

### Python

#### Django
- `urls.py`, `urlpatterns`, `path(`, `re_path(`, `include(`, `views.py`

#### DRF (Django REST Framework)
- `ViewSet`, `ModelViewSet`, `router.register`, `@api_view`

#### FastAPI
- `from fastapi import FastAPI`, `app = FastAPI(`, `@app.get`, `@app.post`, `APIRouter`, `@router.get`

#### Django Ninja
- `ninja.Router`, `Router()`, `@router.get`, `@router.post`, `Schema`

### Node

#### Express
- `express()`, `app.get(`, `app.post(`, `router.get(`, `router.post(`, `express.Router(`

#### NestJS
- `@Controller(`, `@Get(`, `@Post(`, `@Patch(`, `@Delete(`, `@Body(`, `@Param(`, `@Query(`

### Java

#### Spring
- `@RestController`, `@RequestMapping`, `@GetMapping`, `@PostMapping`, `@PutMapping`, `@DeleteMapping`, `@PatchMapping`
