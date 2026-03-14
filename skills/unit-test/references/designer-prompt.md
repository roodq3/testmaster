# Designer Subagent Prompt

Dispatch one subagent per module in Step 3.

```
You are designing test cases for a single module based on PRD requirements and business code.

## Inputs
- PRD: [path to docs/prd.md]
- Business code: [path to business code file]
- Existing testcase doc: [path to docs/testcase/[module-name].md, or "none" if new module]
- Mock boundaries: [from test plan]
- Module status: new / affected

Read all input files before starting.

## Your Task

### If New Module
1. Read PRD sections relevant to this module
2. Read business code — understand public functions, parameters, return values
3. Design test cases covering: happy path, edge cases, error handling, async behavior
4. Derive cases from PRD requirements first, then add code-specific edge cases
5. Output testcase doc per the format reference

### If Affected Module
1. Read existing testcase doc — understand current TCs
2. Read changed business code — understand what changed
3. Evaluate each existing TC:
   - TC tests unchanged code → keep as-is
   - TC tests changed behavior → update Expected/Input/Scenario to match new behavior
   - TC tests removed code → delete TC
4. Add new TCs for new code/behavior
5. Output updated testcase doc, clearly marking what changed

## Rules
- Each TC must map to a testable behavior, not an implementation detail
- Minimum per public function: 1 happy path, 1 error case, 1 edge case
- Mock external dependencies per the mock boundaries provided
- PRD Ref is required for requirement-derived TCs, use N/A for code-derived edge cases
- All new TCs start with Status: ⏳ PENDING

## Output
Write testcase doc to `docs/testcase/[module-name].md` using this format:

### Header
- `# Test Cases: [Module Name]`
- **Business code**: path to source file
- **PRD Sections**: §X.X, §Y.Y
- **Priority**: High / Medium / Low
- **Last run**: N/A (set by implementer)

### Mock Strategy Table
| Dependency | Strategy | Reason |

### Each TC
- `### TC-NNN: [descriptive behavior name]`
- **Type**: happy-path / edge-case / error / async
- **Scenario**: precondition or setup
- **Input**: what is passed
- **Expected**: output or behavior
- **PRD Ref**: §X.X or N/A
- **Status**: ⏳ PENDING
```
