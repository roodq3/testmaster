# Python Unit Test Standards

Use pytest.

## Test Priority

1. **Business logic** — service / usecase / domain layer
2. **Utilities** — utils / helpers / validators
3. **Data layer** — repository / ORM model methods
4. **API serialization** — schema / serializer validation

## Mock Strategy

| Scenario | Approach |
|----------|----------|
| External API | `monkeypatch` or `unittest.mock.patch` |
| Database | mock repository or SQLite in-memory |
| File system | `tmp_path` fixture (pytest built-in) |
| Env variables | `monkeypatch.setenv()` |
| Date/Time | `freezegun` or mock `datetime.now` |
| Random | `monkeypatch.setattr(random, 'random', lambda: 0.5)` |

## Must Follow

- Use `pytest` over `unittest.TestCase` (unless project already has extensive unittest)
- Fixtures over setup/teardown
- `@pytest.mark.parametrize` for parameterized tests
- `pytest-asyncio` + `@pytest.mark.asyncio` for async tests
- Test names should clearly describe behavior

## Common Pitfalls

- **Mutable default args** — `def f(items=[])` shares state across tests
- **Import side effects** — module-level code runs at import, mock before import
- **Floating point** — use `pytest.approx()` not `==`
- **Exception assertions** — `with pytest.raises(ValueError, match="specific message")`
- **Fixture scope** — `session`/`module` scoped fixtures need isolation awareness
