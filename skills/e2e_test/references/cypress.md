# Cypress E2E Test Standards

## Architecture

- Custom commands for common operations like login (`Cypress.Commands.add`)
- Use `cy.session()` to cache login state — avoid full login in every test
- Recommend `@testing-library/cypress` for `findBy*` selectors

## API Mocking

Use `cy.intercept()` + `cy.wait('@alias')` to wait for API responses.

## Differences from Playwright

- Commands are async-queued — can't `const x = cy.get()`, use `.then()`
- No multi-tab/window support
- No native `async/await`

## Common Pitfalls

- **Never use `cy.wait(ms)`** — use `cy.intercept()` + `cy.wait('@alias')`
- Prefer `findByRole`/`findByText` selectors
- `cy.session()` is much faster than `beforeEach` login
- describe / it titles should clearly describe behavior
