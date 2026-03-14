---
name: prd-keeper
description: Use when code changes are complete and PRD/changelog need to be synced, or when the user mentions changing requirements, adding features, removing functionality, or says 'update PRD'.
---

# PRD Keeper

Prevents PRD and code from drifting apart. Syncs after every code change.

## Quick Checklist

1. Collect requirement from session context + classify scope
2. Update PRD in place + bump version
3. Write changelog entry (CLG-XXX: What / Why / Impact / Migration)
4. `git diff --name-only` → fill changed files into changelog Impact field
5. **Commit immediately** (everything together, with `CLG: <number>` tag)

## Core Principle

```
PRD = documentation of actual code behavior (kept in sync, not upfront planning)
Changelog = record of each change (What/Why/Impact), linked by CLG number
CLG number = changelog entry ID (best-effort in git commits, not guaranteed)
```

## Document Layout

```
docs/
├── prd.md                          # Complete PRD (versioned in header)
└── prd-changelog.md                # Structured changelog
```

If the project uses a different PRD location, adapt accordingly. The changelog MUST live next to the PRD.

---

## Step 1: Classify the Change

Get the requirement description from session context (direct input or brainstorm output), then classify:

| Scope | Examples | PRD Edit | Changelog |
|-------|---------|----------|-----------|
| **Tweak** | Fix a typo, clarify wording, adjust a threshold | Inline edit | Short entry |
| **Modify** | Change a rule, swap a component, alter a flow | Section rewrite | Standard entry |
| **Add** | New feature, new module, new user flow | New section | Standard entry |
| **Remove** | Drop a feature, simplify a flow | Delete + note | Standard entry |
| **Rethink** | Change core model, swap architecture, pivot | Major rewrite | Detailed entry |

Ask the user to confirm the scope if ambiguous. One question only:

> "This sounds like a **[scope]** change — I'll update the PRD section on [X], log it in the changelog, and commit. Sound right?"

---

## Step 2: Update the PRD

- **Edit in place** — don't append "v2" sections. The PRD is always the current truth.
- **Bump the version** in the PRD header.
- **Mark deleted content** — don't silently remove. Use strikethrough in the commit, but the final PRD should read clean.
- **Keep PRD structure stable** — don't renumber existing sections. New features get new section numbers.

### PRD Header Format

```markdown
# [Product Name] PRD

> Version: 1.3 | Last updated: 2026-02-27 | Changelog: docs/prd-changelog.md
```

Increment version: Tweak = patch (1.3→1.3.1), Modify/Add/Remove = minor (1.3→1.4), Rethink = major (1.3→2.0).

---

## Step 3: Write the Changelog Entry

Append to `docs/prd-changelog.md`.

Each entry has a **sequential ID** (`CLG-XXX`, three digits, never reset) as changelog entry identifier, plus the PRD version. See `references/changelog-templates.md` for templates of each scope.

### Changelog Entry Rules

1. **What changed**: One or two sentences describing the change.
2. **Why**: Reason for the change (extracted from session context).
3. **Impact on existing code**: Leave blank — filled in Step 4 from `git diff`.
4. **Migration notes**: DB migrations, data format changes, etc. Write "None" if not applicable.

---

## Step 4: Git Diff Review

After editing PRD and changelog, run `git diff --name-only` to get all changed files (code + PRD + changelog).

Fill the code files (excluding `docs/prd.md` and `docs/prd-changelog.md`) into the changelog's **Impact on existing code** field.

---

## Step 5: Commit with CLG Tag

```bash
git add -A
git commit -m "<type>(<scope>): <description>

CLG: <number>"
```

**Key rules:**
- Code + PRD + changelog committed together for clean change boundaries
- prd-keeper commits include `CLG: <number>` trailer (best-effort — manual commits may not have it)
- `git log --grep="CLG: 003"` can help locate related commits, but may be incomplete

See `references/commit-convention.md` for full format and examples.

---

## Special Flows

### "What's changed recently?"

When the user asks about recent changes, read `docs/prd-changelog.md` and summarize the last 3-5 entries.

### "What if we..." (hypothetical change)

Don't update the PRD. Instead:
1. Classify the hypothetical scope
2. Do the impact analysis only
3. Present: "If we made this change, here's what would be affected..."
4. Ask: "Want to proceed with this change?"

### Multiple changes at once

If the user describes several changes:
1. List them and classify each
2. Ask: "Should I batch these as one changelog entry or separate entries?"
3. For batched: one changelog entry with multiple bullets
4. For separate: process each through the full workflow in sequence

---

## Bootstrap

**If `docs/prd.md` doesn't exist**: Create an initial PRD based on the session context and current code structure. Scan source directories, routes, data models, etc. to generate a module overview. Start PRD header at Version 1.0.

**If `docs/prd-changelog.md` doesn't exist**: Create it using the Initial Bootstrap template in `references/changelog-templates.md`.

---

## Red Flags — STOP If You Catch Yourself Thinking These

| Thought | Reality |
|---------|---------|
| "I'll write code first, update PRD later" | Later never comes. This is exactly how PRD drift starts. Update now. |
| "This change is too small for a changelog entry" | Small changes accumulate into big drift. Tweak scope exists for this — use it. |
| "User didn't mention PRD, so I'll skip it" | prd-keeper's job is proactive sync, not waiting to be asked. Code changed = PRD syncs. |
| "Let me commit the code first, PRD next time" | This directly violates the core rule: code + PRD + changelog committed together. |
| "It's just a bug fix, not a requirement change" | Bug fixes change system behavior. Changed behavior = changed requirement. Record it. |
| "No time, I'll catch up on PRD later" | Updating PRD takes 2 minutes. Fixing drift takes 2 hours. Do it now. |
| "This refactor doesn't affect functionality" | Refactors change code structure. The changelog Impact field must reflect those file changes. |

**Any of these thoughts = you are rationalizing skipping the workflow. Stop. Execute the Quick Checklist.**

---

## Anti-patterns

❌ **Changelog says "various updates"** — every entry must say exactly what changed.
❌ **Skipping "Why"** — if you can't explain why, the change shouldn't happen.
❌ **Silently deleting PRD content** — always log removals in changelog.
❌ **prd-keeper commit without CLG tag** — prd-keeper's own commits must include `CLG: <number>`.
❌ **Committing PRD and code separately** — commit together to ensure clean change boundaries.
❌ **Guessing Impact** — get actual changed files from `git diff`, not speculation.
