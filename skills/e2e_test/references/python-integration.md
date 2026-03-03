# Python Integration Test Standards

API-level end-to-end tests verifying the full request chain.

## Framework Detection

| Condition | Test Approach |
|-----------|--------------|
| FastAPI | `TestClient` (httpx) or `pytest-asyncio` + `AsyncClient` |
| Django | `django.test.Client` or `rest_framework.test.APIClient` |
| Flask | `app.test_client()` |

## Must Cover

- Full CRUD flow (create → read → update → delete)
- Param validation (missing/malformed → 422/400)
- Auth (no token → 401, insufficient permissions → 403)
- Pagination, sorting, filtering

## Test Data Management

- FastAPI: `@pytest.fixture` to create test DB, `yield` then cleanup
- Django: `@pytest.mark.django_db` + `TransactionTestCase` or `pytest-django`
- General: SQLite in-memory or TestContainers

## Mock Strategy

- **Don't mock core chain** (Service, Repository) — integration tests exist to test the full chain
- Only mock side-channel deps (notifications, logging, analytics — things that don't affect core flow)
- Core deps with cost should still use real connections (e.g. LLM in agent projects — not mock)
- Use `respx` (httpx) or `responses` (requests) to mock side-channel HTTP calls

## Common Pitfalls

- **SQLite vs PostgreSQL differences** — use TestContainers for critical tests
- **Transaction isolation** — each test gets its own transaction, avoid data leaks
- **Async** — FastAPI async endpoints need `AsyncClient` + `pytest-asyncio`
- **Django** — `@pytest.mark.django_db` must be explicitly marked
- **Fixture dependency order** — DB fixtures before app fixtures
