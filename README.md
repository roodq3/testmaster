[English](README.md) | [中文](README.zh-CN.md)

# TestMaster

A Claude Code plugin for PRD-driven testing. Tests are designed from requirements, reviewed before implementation, then dispatched to subagents.

## Features

- **PRD-driven** — Test cases are derived from product requirements, not guessed from code
- **Design before code** — Test cases are designed and reviewed before any test code is written
- **Review gates** — Impact analysis review and test case review catch problems before implementation
- **Subagent dispatch** — Each module gets its own subagent with clean context, avoiding context pollution
- **Smart mock boundaries** — Identifies external dependencies, auto-mocks side-channel deps, asks you about the rest
- **Two failure modes** — Test code bugs get fixed; business code bugs get recorded, never modified
- **Incremental updates** — On subsequent runs, only affected modules are re-analyzed and re-tested
- **Interruption recovery** — Progress saved to plan files and testcase docs; pick up where you left off
- **Framework-agnostic** — Works with any language/framework

## Skills

| Command | What it does |
|---------|-------------|
| `/testmaster:unit-test` | PRD-driven unit testing with review gates, subagent dispatch per module |
| `/testmaster:prd-keeper` | Keeps PRD in sync when requirements are added or changed, generates structured changelog with CLG-tagged commits |

## Install

> Installation differs by platform. Claude Code and Cursor have built-in plugin systems. Codex and OpenCode require manual setup.

### Claude Code

```bash
# Step 1: Register the marketplace
/plugin marketplace add roodq3/testmaster

# Step 2: Install the plugin
/plugin install testmaster@testmaster
```

### Cursor

In Cursor Agent chat:

```
/plugin-add testmaster
```

### Codex

Paste this into a Codex session:

```
Fetch and follow instructions from https://raw.githubusercontent.com/roodq3/testmaster/refs/heads/main/.codex/INSTALL.md
```

Or install manually:

```bash
git clone https://github.com/roodq3/testmaster.git ~/.codex/testmaster
mkdir -p ~/.agents/skills
ln -s ~/.codex/testmaster/skills ~/.agents/skills/testmaster
```

### OpenCode

Paste this into an OpenCode session:

```
Fetch and follow instructions from https://raw.githubusercontent.com/roodq3/testmaster/refs/heads/main/.opencode/INSTALL.md
```

Or install manually:

```bash
git clone https://github.com/roodq3/testmaster.git ~/.config/opencode/testmaster
mkdir -p ~/.config/opencode/skills
ln -s ~/.config/opencode/testmaster/skills ~/.config/opencode/skills/testmaster
```

### Update

| Platform | Command |
|----------|---------|
| Claude Code | `/plugin update testmaster` |
| Cursor | `/plugin-update testmaster` |
| Codex | `cd ~/.codex/testmaster && git pull` |
| OpenCode | `cd ~/.config/opencode/testmaster && git pull` |

### Uninstall

| Platform | Command |
|----------|---------|
| Claude Code | `/plugin uninstall testmaster` |
| Cursor | `/plugin-remove testmaster` |
| Codex | `rm -rf ~/.agents/skills/testmaster ~/.codex/testmaster` |
| OpenCode | `rm -rf ~/.config/opencode/skills/testmaster ~/.config/opencode/testmaster` |

## How It Works

### Prerequisites

- `docs/prd.md` must exist in your project (`prd-keeper` auto-creates and maintains it during development)

### PRD Keeper Flow

Every code change triggers a sync cycle:

1. Classify the change scope (Tweak / Modify / Add / Remove / Rethink)
2. Update `docs/prd.md` in place and bump the version
3. Append a changelog entry to `docs/prd-changelog.md` with a sequential ID (`CLG-001`, `CLG-002`, ...)
4. Run `git diff --name-only` to fill the changelog's **Impact on existing code** field with actual changed files
5. Commit code + PRD + changelog together, with a `CLG: <number>` trailer in the commit message

#### Structured Changelog

Each entry in `docs/prd-changelog.md` has a unique `CLG-XXX` ID and records:

- **What changed** — one or two sentences
- **Why** — the reason behind the change
- **Impact on existing code** — affected files (from `git diff`, not guessed)
- **Migration notes** — DB migrations, data format changes, etc.

#### CLG-Tagged Commits

Commits made by prd-keeper include a `CLG: <number>` trailer linking back to the changelog entry:

```
feat(auth): add OAuth2 login flow

CLG: 003
```

This enables traceability — `git log --grep="CLG: 003"` finds all commits related to a specific changelog entry.

### Unit Test Flow

```
Read PRD + changelog → Impact analysis → Impact review (gate)
        ↓
Framework setup (if needed)
        ↓
Test plan + case design (per module) → Test case review (gate)
        ↓
Write plan file → Dispatch implementer subagents (per module)
        ↓
Full suite run → Coverage check → Summary
```

1. Reads PRD and changelog to understand requirements and recent changes
2. Scans codebase, maps modules and dependencies, produces affected module list
3. Impact reviewer subagent validates the affected list (first run: skipped, all modules are new)
4. Sets up test framework if not already present
5. Dispatches designer subagent per module to create testcase docs (`docs/testcase/`)
6. Testcase reviewer subagent validates coverage and mock strategy
7. Dispatches implementer subagent per module — writes tests, runs them, fix loop (max 5 rounds)
8. Runs full test suite, checks coverage, outputs summary

### Source Code Boundary

When a test fails, TestMaster follows strict rules:

- **Test code bug** (wrong mock, bad assertion) → fixes the test
- **Business code bug** (actual defect) → records the finding, never modifies business code
- **Can't determine** → treats as business code bug, marks for review

### Interruption Recovery

All progress is saved to `docs/plans/YYYY-MM-DD-HHmmss-unit-test.md` and `docs/testcase/`. If your session is interrupted:

- TestMaster finds the latest plan file and testcase docs
- Locates the first uncompleted module
- Continues from there

## Contributing

Open an issue with: framework name, source file, and what went wrong.

## License

MIT
