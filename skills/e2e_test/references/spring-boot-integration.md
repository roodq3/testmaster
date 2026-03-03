# Spring Boot Integration Test Standards

API-level end-to-end tests verifying the full chain: Controller → Service → Repository → Database.

## Test Setup

- `@SpringBootTest` + `@AutoConfigureMockMvc` + `@ActiveProfiles("test")`
- Use H2 in-memory or TestContainers (real database)

## Must Cover

- Full CRUD flow (create → read → update → delete)
- Param validation (empty/malformed → 400)
- Business rule validation (duplicate data → 409, etc.)
- Auth flow (no token → 401, insufficient permissions → 403)
- Pagination queries

## Test Data Management

- `@BeforeEach` cleanup or `@Transactional` auto-rollback
- `@Sql` annotation to load test data
- Builder pattern for test entities

## Mock Strategy

- **Don't mock core chain** (Service, Repository) — integration tests exist to test the full chain
- Only mock side-channel deps (notifications, logging, analytics — things that don't affect core flow)
- Core deps with cost should still use real connections (sandbox if available, not mock)

## Common Pitfalls

- **H2 and MySQL/PG SQL dialects are not fully compatible** — use TestContainers for critical tests
- Use `@SpringBootTest(webEnvironment = RANDOM_PORT)` to avoid port conflicts
- `@Transactional` rolls back everything — be aware of its impact on assertions
- Async endpoints: use Awaitility to wait for results
- `@DisplayName` should clearly describe behavior
