# Changelog Entry Templates

Quick-reference templates for different change scopes.
CLG number = previous entry +1, three digits, never reset.

## Initial Bootstrap

If `docs/prd-changelog.md` doesn't exist yet, create it with this template:

```markdown
# PRD Changelog

All changes to the product requirements document are logged here.

Format: CLG-XXX [version] - date, scope, what/why/impact.

---

## CLG-001 [1.0] - YYYY-MM-DD

**Scope**: Initial | **Sections affected**: All

### What changed
Initial PRD created.

### Why
Project kickoff.

### Impact on existing code
N/A — greenfield.

### Migration notes
N/A
```

## Tweak (patch version)

```markdown
## CLG-004 [1.3.1] - YYYY-MM-DD

**Scope**: Tweak | **Sections affected**: X.X

### What changed
[One sentence describing the clarification/fix]

### Why
[Why this needed clarifying]

### Impact on existing code
[Filled from `git diff --name-only` — see Step 4]

### Migration notes
None
```

## Modify (minor version)

```markdown
## CLG-005 [1.4] - YYYY-MM-DD

**Scope**: Modify | **Sections affected**: X.X, X.X

### What changed
[2-3 sentences: what rule/component/flow changed and how]

### Why
[What problem or feedback drove this change]

### Impact on existing code
[Filled from `git diff --name-only` — see Step 4]

### Migration notes
[DB migration needed? Data format change? Or "None"]
```

## Add (minor version)

```markdown
## CLG-006 [1.5] - YYYY-MM-DD

**Scope**: Add | **Sections affected**: NEW §X.X

### What changed
New feature: [Feature name]
- [Key aspect 1]
- [Key aspect 2]
- [Key aspect 3]

### Why
[User need / business reason for the new feature]

### Impact on existing code
[Filled from `git diff --name-only` — see Step 4]

### Migration notes
[New DB tables? New env vars? Or "None"]
```

## Remove (minor version)

```markdown
## CLG-007 [1.6] - YYYY-MM-DD

**Scope**: Remove | **Sections affected**: §X.X (removed)

### What changed
Removed feature: [Feature name]
Former PRD §X.X content has been deleted.

### Why
[Why this feature is no longer needed]

### Impact on existing code
[Filled from `git diff --name-only` — see Step 4]

### Migration notes
[Data cleanup needed? Drop tables? Or "None"]
```

## Rethink (major version)

```markdown
## CLG-008 [2.0] - YYYY-MM-DD

**Scope**: Rethink | **Sections affected**: §X, §Y, §Z (major rewrite)

### What changed
Core change: [One-line summary of the paradigm shift]

Detail:
1. [Major change 1: before → after]
2. [Major change 2: before → after]
3. [Major change 3: before → after]

### Why
[Strategic reason for the rethink]

### Impact on existing code
[Filled from `git diff --name-only` — see Step 4]

### Migration notes
[Detailed migration plan — DB, data, configs]
```
