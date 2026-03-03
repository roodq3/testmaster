---
name: testmaster:e2e_test
description: "Write and improve E2E end-to-end tests. Supports Spring Boot (REST API integration tests), Python (FastAPI/Django/Flask), Vue/React/H5 (Playwright/Cypress). Trigger on phrases like 'end-to-end', 'E2E', 'integration test'."
---

# E2E Test Skill

## Step 1: Detect Tech Stack and E2E Framework

Scan the project root to identify the tech stack. If a matching reference file exists in `references/`, read it for framework-specific best practices and pitfalls. If no reference file matches, proceed with general testing principles — reference files are optional enhancements, not prerequisites. Install Playwright by default if no E2E framework is found in a frontend project.

## Step 2: Identify User Flows and Generate Plan

**Supplement mode**: User specified a flow (e.g. `/e2e-test product favorites flow`) → read the latest plan file, append the new flow, skip to Step 5.

**Full scan mode**: User didn't specify →

1. Read route config and page structure
2. Scan existing E2E test files to identify already-covered flows
3. Identify uncovered flows, sort by priority:
   - 🔴 P0: Auth flows, core business flows
   - 🟡 P1: CRUD, search/filter
   - 🟢 P2: Edge cases (permission denied, error pages)
4. Write plan to `docs/plans/YYYY-MM-DD-HHmmss-e2e-test.md`, create Tasks API tasks

## Step 3: Assess Mock Boundaries

1. List all external dependencies involved in the flows under test
2. Obvious side-channel dependencies (notifications, logging, analytics) — auto-mark as Mock
3. **List remaining dependencies and ask the user to confirm each one**: Mock or real
4. For dependencies the user chooses to connect for real: list detected config items (connection URLs, keys, etc.) and **ask the user to confirm the config is correct**. Pause and prompt if anything is missing.

Write the assessment to the plan file header.

## Step 4: Configure Test Environment

- Frontend: confirm dev server command and base URL, create config file
- Spring Boot / Python: confirm test database config

## Step 5: Write E2E Tests Per Flow

1. Scan `e2e/pages/` for existing Page Objects — reuse them, only create new POs for new pages
2. Each flow must cover: full happy path, key error scenarios, boundary conditions, data cleanup after test
3. Stability rules: explicit waits (no sleep), selector priority `data-testid` > `role` > `text`, mock side-channel deps only, tests must be independent
4. Run after each flow, fix loop max 5 rounds: test bug → fix test; obvious source bug → fix source and log; design issue → don't fix, mark for user review
5. Update Tasks API and plan file checkbox

## Step 6: Full Suite Run and Report

1. **Run all E2E tests** (old + new) — fix regressions from new code too
2. Generate report at `docs/plans/YYYY-MM-DD-HHmmss-e2e-report.md` with: test environment, mock strategy, per-flow result table, Page Objects list, fix log

## Resume After Interruption

Read the latest `*-e2e-test.md` in `docs/plans/`, continue from the first unchecked flow.

## General Principles

- Test names should describe user behavior clearly
- Frontend E2E must use Page Object Model
- Test data via fixtures, never depend on specific database state
- Save screenshots on failure
- Ensure tests can run in parallel
