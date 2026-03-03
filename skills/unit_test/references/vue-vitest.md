# Vue Unit Test Standards

## Test Priority

1. **Composables** — pure logic, most stable, test first
2. **Pinia Store** — `setActivePinia(createPinia())` in beforeEach
3. **Components** — test business logic, not framework behavior
4. **Utils / API layer**

## Mock Strategy

| Scenario | Approach |
|----------|----------|
| fetch | `globalThis.fetch = vi.fn().mockResolvedValue(...)` |
| Third-party libs | `vi.mock('axios', ...)` |
| Pinia | `createTestingPinia({ createSpy: vi.fn })` |
| Router | `global.stubs: { RouterLink: true }` |
| Timers | `vi.useFakeTimers()` |
| Date | `vi.setSystemTime(new Date('2024-01-01'))` |
| localStorage | `vi.stubGlobal(...)` |

## Must Follow

- describe / it names should clearly describe behavior
- Simple components use `mount`, complex nesting use `shallowMount`
- Create `createWrapper` factory for components with shared config

## Common Pitfalls

- After modifying reactive data, `await nextTick()`
- `wrapper.emitted()` returns array of arrays — watch the nesting
- Stub `RouterLink`/`RouterView` via `global.stubs`
- Don't test Vue framework behavior itself
