[English](README.md) | [中文](README.zh-CN.md)

# TestMaster

A Claude Code plugin that automatically generates, runs, and fixes tests for your project.

## Features

- **Auto-detect tech stack** — Scans your project and picks the right test framework automatically
- **Write tests that pass** — Generates tests, runs them, and enters a fix loop (max 5 rounds) until they pass
- **Incremental by default** — Only writes tests for new/uncovered code, never duplicates existing tests
- **Full regression check** — Runs the entire test suite every time, catching breakages from new code
- **Coverage enforcement** — Checks line (≥ 80%) and branch (≥ 70%) coverage, adds tests if below threshold
- **Smart mock boundaries** — Auto-mocks side-channel dependencies, asks you to decide on the rest
- **Interruption recovery** — Saves progress to plan files on disk; pick up where you left off after any interruption
- **Framework-agnostic** — Works with any language/framework; built-in reference files for popular stacks provide extra quality
- **E2E flow supplement** — Run `/testmaster:e2e_test <flow description>` to add specific missing flows without re-scanning

## Skills

| Command | What it does |
|---------|-------------|
| `/testmaster:unit_test` | Generates unit tests file by file with coverage enforcement |
| `/testmaster:e2e_test` | Identifies user flows and writes end-to-end tests with Page Object Model |
| `/testmaster:e2e_test <description>` | Appends a specific flow to existing E2E tests |

## Built-in Reference Files

Best practices and common pitfalls for:

| Unit Test | E2E Test |
|-----------|----------|
| Spring Boot (JUnit5 + Mockito) | Playwright |
| Vue (Vitest + Vue Test Utils) | Cypress |
| React (Vitest/Jest + Testing Library) | Spring Boot Integration |
| TypeScript (Vitest) | Python (FastAPI / Django / Flask) |
| Python (pytest) | |
| Vanilla H5 (Vitest + jsdom) | |

No reference file for your stack? **TestMaster still works.** Reference files are optional enhancements — they help Claude avoid framework-specific pitfalls. PRs welcome to add more.

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

### Unit Test Flow

```
Detect stack → Check infrastructure → Scan & plan → Write per file → Run per file → Fix loop
                                                                              ↓
                                                              Full suite run → Coverage check → Summary
```

1. Detects your tech stack, reads matching reference files (if available)
2. Verifies test framework is installed, installs if missing
3. Scans all source files, skips those with existing tests, generates a plan file
4. For each file: writes tests → runs only that file's tests → fix loop (max 5 rounds)
5. Runs the full test suite, checks coverage, prints summary to terminal

### E2E Test Flow

```
Detect stack → Identify flows → Assess mock boundaries → Configure env → Write per flow → Fix loop
                                         ↓                                        ↓
                                  Ask user to confirm                  Full suite run → Report
```

1. Detects tech stack and E2E framework (installs Playwright if none found)
2. Identifies user flows by priority (P0 auth/core → P1 CRUD/search → P2 edge cases)
3. Lists all external dependencies — auto-mocks side-channel deps, asks you about the rest
4. For real dependencies: shows detected config, asks you to confirm it's correct
5. Writes tests per flow using Page Object Model, reuses existing Page Objects
6. Runs full suite, generates a Markdown report

### Interruption Recovery

All progress is saved to `docs/plans/YYYY-MM-DD-HHmmss-*.md`. If your session is interrupted:

- TestMaster finds the latest plan file
- Locates the first uncompleted task
- Continues from there

### Source Code Fixes

When a test fails, TestMaster follows these rules:

- **Test bug** → fixes the test
- **Obvious source bug** (null not handled, index out of bounds) → fixes the source and logs it in the plan
- **Design issue or unclear** → does not modify source, marks it for your review

## Contributing

### Add a reference file

Create a file in `skills/unit_test/references/` or `skills/e2e_test/references/`:

- Only include what Claude tends to get wrong — pitfalls, mock strategies, conventions
- Don't include code examples, install commands, or config templates
- Keep it under 40 lines
- See existing files for the format

### Report issues

Open an issue with: framework name, source file, and what went wrong.

## License

MIT
