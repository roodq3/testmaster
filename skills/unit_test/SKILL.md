---
name: testmaster:unit_test
description: "Write and improve unit tests. Supports Spring Boot (JUnit5+Mockito), Vue (Vitest+Vue Test Utils), React (Vitest/Jest+Testing Library), TypeScript (Vitest), Python (pytest), vanilla H5 (Vitest+jsdom). Trigger on phrases like 'write tests', 'add tests', 'test coverage'."
---

# Unit Test Skill

## Step 1: Detect Tech Stack

Scan the project root to identify the tech stack. If a matching reference file exists in `references/`, read it for framework-specific best practices and pitfalls. If no reference file matches, proceed with general testing principles — reference files are optional enhancements, not prerequisites.

## Step 2: Check Test Infrastructure

Verify the test framework is installed and configured. Install if missing. Run once to confirm it works.

## Step 3: Generate Test Plan

1. Glob scan all source files, exclude those with existing tests (`__tests__/`, `*.test.*`, `*.spec.*`, `*Test.java`, `test_*.py`, `*_test.py`)
2. On incremental runs, only plan for new source files without tests
3. Sort by priority: high (service/domain/store/utils) → medium (repository/api/components with logic) → low (config/constants/types/presentational)
4. Write plan to `docs/plans/YYYY-MM-DD-HHmmss-unit-test.md`, create Tasks API tasks

## Step 4: Write Tests File by File

For each file:

1. Read source code — understand signatures, branches, side effects
2. Write tests covering: happy path, boundary conditions, error handling, all branches, async behavior, external dependency mocks
3. **Run only the current file's tests** (not full suite)
4. Fix loop (max 5 rounds): test bug → fix test; obvious source bug → fix source and log in plan; design issue → don't fix, mark for user review
5. Update Tasks API status and plan file checkbox

## Step 5: Full Suite Run and Summary

1. **Run the entire test suite** (old + new) — fix regressions from new code too
2. Check for cross-test side effects (shared state, uncleaned mocks)
3. Coverage check: line ≥ 80%, branch ≥ 70% — add tests if below threshold
4. Print summary to terminal (tech stack, file count, test count, pass count, coverage, fix log)

## Resume After Interruption

Read the latest `*-unit-test.md` in `docs/plans/`, continue from the first unchecked task.

## General Principles

- AAA pattern: Arrange → Act → Assert
- Tests must be independent, no execution order dependency
- Mock external dependencies, never mock the unit under test
- Test names should describe behavior clearly
- Don't test framework internals (no testing getters/setters or third-party library behavior)
