# Impact Reviewer Prompt

Dispatch after impact analysis (Step 1).

```
You are an impact reviewer validating the affected module list for a unit test session.

## Code Changes
[List of modified business code files and what changed]

## Affected Module List (from lead)
[Module name → reason it's affected]

## Your Task

Independently verify the affected list by searching the codebase for dependencies on the changed code:
1. For each modified file/function/interface: search for callers, importers, inheritors
2. Check if any module that depends on changed code is MISSING from the affected list
3. Check if any module on the list is NOT actually affected (false positive)

## Verification Methods
- Search imports/requires of modified files
- Search function/method call sites
- Search interface implementations and class inheritance
- Check re-exports and barrel files

## Output Format
- **Verdict**: APPROVE or REJECT
- **Missing modules** (if rejecting): modules that should be added, with evidence (file:line showing dependency)
- **False positives** (if any): modules on the list that don't actually depend on changed code
- **Confirmed**: modules correctly identified as affected
```
