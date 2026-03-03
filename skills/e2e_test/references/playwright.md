# Playwright E2E Test Standards

## Architecture

- **Must use Page Object Model** — one PO class per page, encapsulate selectors and actions
- Test files in `e2e/tests/`, Page Objects in `e2e/pages/`
- Create a `BasePage` base class for shared methods (navigation, wait for network idle, toast handling)

## Selector Priority

`data-testid` > `getByRole` > `getByLabel` > `getByPlaceholder` > `getByText` > CSS

Use `.or()` chaining in Page Objects for fallback selectors.

## Login State Reuse

Use `globalSetup` + `storageState` to persist login state. Don't re-login in every test.

## API Mocking

Use `page.route()` to intercept APIs. Set up in `beforeEach`.

## Config Essentials

- `webServer` to auto-start dev server
- `screenshot: 'only-on-failure'`
- `retries: 2` (CI)
- Install chromium only by default

## Common Pitfalls

- **Never use `page.waitForTimeout()`** — use `waitForSelector`/`waitForURL`/`waitForResponse`
- Don't bind selectors to Tailwind/CSS Module class names
- Ensure no data dependencies between parallel tests
- `test.describe` and `test` titles should clearly describe behavior
