# TypeScript Unit Test Standards

For framework-free pure TS projects (Node.js services, SDKs, utility libraries).

## Test Focus

- Pure functions: normal input, boundary values, invalid input
- Classes / OOP: lifecycle, state changes, events
- Async functions: success, failure, timeout, retry
- Generics / type guards: test with different type parameters

## Mock Strategy

| Scenario | Approach |
|----------|----------|
| Modules | `vi.mock('./module', ...)` |
| File system | `vi.mock('fs/promises')` |
| Env variables | `vi.stubEnv('NODE_ENV', 'test')` |
| Timers | `vi.useFakeTimers()` |
| Date | `vi.setSystemTime(...)` |
| Random | `vi.spyOn(Math, 'random').mockReturnValue(0.5)` |

## Common Pitfalls

- Floating point: use `toBeCloseTo` not `toBe`
- Singletons / global state: `new` a fresh instance in beforeEach
- Error assertions: `toThrow('specific message')` not just `toThrow()`
- `it.each` for multiple input sets to avoid duplication
