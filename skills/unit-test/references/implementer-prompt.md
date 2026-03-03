# Implementation Subagent Prompt

Dispatch one subagent per module in Step 4.

```
You are implementing unit tests for a single module based on a pre-designed test case document.

## Inputs
- Test case doc: [path to docs/testcase/[module-name].md]
- Business code: [path to business code file]

Read both files before starting.

## Your Task
1. Read the test case design — implement each TC-XXX as an actual test
2. Follow project conventions for file naming, directory structure, imports
3. Use AAA pattern: Arrange → Act → Assert
4. Run only this file's tests
5. Verification gate: read full test output, confirm each case individually.
   Tests may legitimately fail due to business code bugs — that's expected.
6. Fix loop (max 5 rounds) — diagnose each failure:
   - Test code bug (wrong mock, bad assertion, typo) → fix test code
   - Business code bug (actual defect in application) → don't touch, mark as failure, record
   - Can't determine → treat as business code bug (don't modify)
   - Same TC fails 3 consecutive rounds → stop, mark as 🔍 NEEDS_REVIEW

## Rules
- Test names must match the TC descriptions (readable behavior names)
- Mock external dependencies per the mock strategy in the test case doc
- Never mock the unit under test
- Inject time, randomness, and I/O — never depend on real clock/network/filesystem
- Use parameterized tests for multiple input/output combinations
- Tests must be independent, no execution order dependency

## Anti-patterns — stop immediately if you catch yourself doing these
- Mock returns X, assert result is X → testing the mock, not logic
- assert result is not None → meaningless assertion
- Expected values copied from business code → use known-correct values
- Modifying business code to make tests pass → never touch business code
- Adding TCs beyond what's designed → only implement what's in the testcase doc

## Self-check
- "Should pass now" → run it and read output
- "I'm confident" → confidence ≠ evidence
- "I just ran it" → that was before your last change
- "Error is obvious" → read the full stack trace
- "This file is simple" → simple files get lazy tests

## Write Results Back
Update `docs/testcase/[module-name].md`:
1. Set `Last run` in header to today's date
2. Set Status on each TC:
   - `✅ PASSED` — test passed
   - `❌ FAILED — [reason]` — business code bug, add `Actual` field showing what happened
   - `🔍 NEEDS_REVIEW` — same test failed 3 consecutive rounds, needs human review

Failed tests stay in test code as-is (no skip/xfail). Tests reflect real system state.

## Existing Test Files
If a test file already exists for this module:
- Read it first, understand existing tests
- Add new tests for new TCs, update tests for changed TCs
- Do not rewrite tests for unchanged TCs

## Report Back
- Files created/modified (test code only)
- Test count, pass count, fail count
- Business code bugs found (list with evidence, don't fix)
- Status: passed / needs_review
```
