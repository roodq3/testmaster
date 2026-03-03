# PRD Commit Convention

Commits made by prd-keeper include a CLG number trailer. Manual commits may not have one — CLG in git is best-effort, not guaranteed.

## Commit Message Format

```
<type>(<scope>): <description>

CLG: <number>
```

**type** follows conventional commits: `feat`, `fix`, `refactor`, `test`, `docs`, `chore`

**Examples:**

```bash
# Code + PRD + changelog committed together
feat(auth): add OAuth2 login flow
CLG: 003

# Pure bug fix (also linked to requirement)
fix(payment): handle empty cart on checkout
CLG: 003

# Cross-change commit (rare, try to avoid)
refactor(user-profile): unify address validation logic
CLG: 003, 002
```

## Commit Rules

- **Code + PRD + changelog committed together** for clean change boundaries
- prd-keeper commits include `CLG: <number>` trailer (manual commits may lack it)
- One CLG number can correspond to multiple commits (implementation + tests + docs)

## Traceability (best-effort)

CLG tags in git are auxiliary — manual commits, rebases, and squashes may break the chain. Use as a helpful lookup, not a reliable audit trail.

```bash
# Find commits for a changelog entry (may be incomplete)
git log --grep="CLG: 003"

# See what change drove the last edit to a file
git log --oneline -1 -- src/auth/oauth.ts

# Review all PRD changes (reliable — tracks file, not tags)
git log --oneline -- docs/prd.md docs/prd-changelog.md
```
