# Vanilla H5 Unit Test Standards

For framework-free HTML/CSS/JS projects. Uses Vitest + jsdom.

## Test Focus

- DOM manipulation functions: create, modify, query elements
- Event handlers: form submit, click, keyboard
- localStorage / sessionStorage
- fetch / AJAX calls
- Timers / animations (debounce, countdown)

## Mock Strategy

| Scenario | Approach |
|----------|----------|
| DOM | `document.body.innerHTML = '...'` reset in beforeEach |
| fetch | `globalThis.fetch = vi.fn()` |
| localStorage | jsdom built-in, `localStorage.clear()` to reset |
| alert/confirm | `vi.stubGlobal('alert', vi.fn())` |
| Timers | `vi.useFakeTimers()` + `vi.useRealTimers()` |
| Canvas | Manually mock `getContext('2d')` |

## Must Follow

- If no `package.json`, run `npm init -y` first
- Source files must use `export` for test imports
- Trigger events via `new Event()` / `new CustomEvent()` + `dispatchEvent()`

## Common Pitfalls

- **Reset DOM in beforeEach for every test** — otherwise tests pollute each other
- jsdom doesn't support real CSS rendering or `window.scrollTo`
- Access globals via `globalThis.xxx`
- After `vi.useFakeTimers()`, must call `vi.useRealTimers()` in afterEach
