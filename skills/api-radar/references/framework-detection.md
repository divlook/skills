# Framework Routing Hints

Use dependency and build manifests to select the relevant framework hints. Find route composition first, then trace the request and response schema types named by the route.

## Python

### Django
- `urls.py`, `urlpatterns`, `path(`, `re_path(`, `include(`, `views.py`

### DRF (Django REST Framework)
- `ViewSet`, `ModelViewSet`, `router.register`, `@api_view`

### FastAPI
- `from fastapi import FastAPI`, `app = FastAPI(`, `@app.get`, `@app.post`, `APIRouter`, `@router.get`

### Django Ninja
- `ninja.Router`, `Router()`, `@router.get`, `@router.post`, `Schema`

## Node

### Express
- `express()`, `app.get(`, `app.post(`, `router.get(`, `router.post(`, `express.Router(`

### NestJS
- `@Controller(`, `@Get(`, `@Post(`, `@Patch(`, `@Delete(`, `@Body(`, `@Param(`, `@Query(`

## Java

### Spring
- `@RestController`, `@RequestMapping`, `@GetMapping`, `@PostMapping`, `@PutMapping`, `@DeleteMapping`, `@PatchMapping`
