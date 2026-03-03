# React Unit Test Standards

## Test Priority

1. **Custom Hooks** — `renderHook` + `act`, pure logic, most stable
2. **State Management** — Zustand: `useStore.setState()` reset; Redux: `configureStore`
3. **Components** — Testing Library, test user-visible behavior
4. **Utils / API layer**

## Mock Strategy

| Scenario | Approach |
|----------|----------|
| fetch | `globalThis.fetch = vi.fn()` |
| React Router | Wrap with `<MemoryRouter>` |
| Child components | `vi.mock('../Child', ...)` |
| Zustand | `useStore.setState()` |
| IntersectionObserver | `vi.stubGlobal(...)` |

## Must Follow

- **`userEvent` over `fireEvent`** — `userEvent.setup()` is more realistic
- **Query priority**: `getByRole` > `getByLabelText` > `getByText` > `getByTestId`
- Async: use `waitFor` or `findBy*`
- Components with Context: create `renderWithProviders` factory
- describe / it names should clearly describe behavior

## Common Pitfalls

- Don't test implementation details (don't assert state variable names) — test user-visible behavior
- Wrap state updates in `act()`
- Cleanup is automatic, no manual cleanup needed
- `it.each` for multiple input sets
