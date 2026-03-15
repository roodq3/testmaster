# Test Case Document Format

Each module gets its own file at `docs/testmaster/testcase/[module-name].md`.

## Template

```markdown
# Test Cases: [Module Name]

**Business code**: `path/to/source`
**PRD Sections**: §X.X, §Y.Y
**Priority**: High / Medium / Low
**Last run**: YYYY-MM-DD or N/A

## Mock Strategy

| Dependency | Strategy | Reason |
|-----------|----------|--------|
| Database | Mock | External I/O |
| UserService | Real | Under test |
| EmailAPI | Mock | Side effect |

## Test Cases

### TC-001: [descriptive behavior name]

- **Type**: happy-path / edge-case / error / async
- **Scenario**: [precondition or setup]
- **Input**: [what is passed]
- **Expected**: [output or behavior]
- **PRD Ref**: §X.X
- **Status**: ✅ PASSED

### TC-002: [descriptive behavior name]

- **Type**: edge-case
- **Scenario**: [precondition]
- **Input**: [what is passed]
- **Expected**: [output or behavior]
- **Actual**: [what actually happened]
- **PRD Ref**: §X.X
- **Status**: ❌ FAILED — [business code bug description]

### TC-003: [descriptive behavior name]

- **Type**: error
- **Scenario**: [precondition]
- **Input**: [what is passed]
- **Expected**: [output or behavior]
- **PRD Ref**: N/A (code-derived edge case)
- **Status**: ⏳ PENDING

## Notes

[Any special setup, environment requirements, or known issues]
```

## Status Values

| Status | Meaning |
|--------|---------|
| `⏳ PENDING` | Designed but not yet implemented/run |
| `✅ PASSED` | Test passed |
| `❌ FAILED — [reason]` | Failed due to business code bug |
| `🔍 NEEDS_REVIEW` | Consecutive failures, needs human review |

- **Actual** field: only add when Status is ❌ FAILED — shows what business code actually returned vs Expected
- **Last run** in header: update each time tests are executed

## Naming Convention

- File name = module name in kebab-case: `user-service.md`, `payment-processor.md`
- Test case IDs are sequential per file: TC-001, TC-002, ...
- IDs are for reference only — test code uses descriptive names

## Guidelines

- Derive test cases from PRD requirements first, then add code-specific edge cases
- Each test case should map to a testable behavior, not an implementation detail
- Group related cases under a shared setup/context if they share preconditions
- Minimum per public function: 1 happy path, 1 error case, 1 edge case
- PRD Ref is optional — code-derived edge cases may not have a PRD section, use `N/A`
