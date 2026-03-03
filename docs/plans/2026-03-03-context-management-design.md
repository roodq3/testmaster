# Context Management Optimization Design

**Goal:** Prevent context pollution in long test generation sessions by using subagent isolation + plan file as context snapshot.

**Architecture:** Main agent handles global setup (detect, plan) and writes context to plan file header. Each source file/flow is tested by a fresh subagent that receives only the context snapshot + current file. Main agent aggregates results and runs full suite.

---

## Problem

Current testmaster processes all files sequentially in a single context window. For large projects (50+ files), early file contents, test code, and fix-loop traces accumulate and degrade later test quality. Cross-session recovery only restores progress, not the detected tech stack or project conventions.

## Solution: Main Agent + Subagent Split

### Phase 1: Main Agent (Step 1-3)

No change to the detection and planning workflow. One addition:

**Plan file header now stores a context snapshot:**

```markdown
## Context
- Tech stack: [detected]
- Test framework: [detected]
- Conventions: [learned from 2-3 existing test files]
- Test run command: [full suite]
- Single file run command: [pattern]
```

This header is the ONLY context passed to subagents. It replaces "working memory" as the source of truth.

### Phase 2: Subagent Dispatch (Step 4)

Main agent loops through unchecked files in the plan:

```
For each unchecked file:
  1. Read plan header (context snapshot)
  2. Read the source file
  3. Dispatch fresh subagent with:
     - Context snapshot (from plan header)
     - Source file content
     - Rules (General Principles + Anti-Patterns + Rationalization Prevention, inline)
     - Single file run command
  4. Receive subagent result
  5. Update plan checklist line
```

Subagent prompt template:

```
You are writing unit tests for a single source file.

## Context
{plan_header_context}

## Source File
Path: {file_path}
{file_content}

## Your Job
1. Write tests following project conventions above
2. Run only this file's tests: {single_file_run_command}
3. Verify gate: read FULL output, confirm exit code 0 and all passed
4. Fix loop (max 5 rounds):
   - Error in test code → fix test
   - Error in source code → don't fix, report finding
   - Ambiguous → treat as source bug
   - 3 failures → STOP, report "needs manual review"

## Rules
{general_principles}
{anti_patterns}
{rationalization_prevention}

## Report Back
- Files created/modified
- Test count and pass count
- Source bugs found (list, don't fix)
- Status: passed / needs_review / skipped
```

### Phase 3: Main Agent (Step 5)

After all subagents complete:
1. Run full test suite
2. Completion checklist
3. Print summary

### Cross-Session Recovery

Resume reads the plan file and gets:
- Context snapshot from header (tech stack, conventions, run command)
- Progress from checklist (which files done, which failed)
- Findings log (source bugs, skipped files)

No context is lost between sessions.

## Changes to SKILL.md

### unit_test/SKILL.md

1. **Step 3**: Add "Write context snapshot to plan file header"
2. **Step 4**: Change from direct execution to subagent dispatch loop
3. **Step 4 subagent prompt**: Inline General Principles, Anti-Patterns, Rationalization Prevention

### e2e_test/SKILL.md

Same pattern, adapted for flows:
1. **Step 2**: Add context snapshot to plan file header
2. **Step 5**: Change from direct execution to subagent dispatch loop
3. **Step 5 subagent prompt**: Inline E2E-specific rules

### Plan File Format (both skills)

Header with context snapshot + markdown checklist with status markers.

## What Stays the Same

- All General Principles, Anti-Patterns, Rationalization Prevention content unchanged
- Fix loop logic unchanged (just moves into subagent prompt)
- Verification gate unchanged
- Escalation trigger unchanged
- Completion checklist unchanged
- E2E mock boundary assessment unchanged (stays in main agent)
