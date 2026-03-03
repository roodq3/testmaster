# TestMaster — Codex Installation

Enable TestMaster skills in Codex via native skill discovery. Clone and symlink.

## Install

Clone the repository:

```bash
git clone https://github.com/roodq3/testmaster.git ~/.codex/testmaster
```

Create a symlink so Codex discovers the skills:

```bash
mkdir -p ~/.agents/skills
ln -s ~/.codex/testmaster/skills ~/.agents/skills/testmaster
```

Restart Codex to discover the skills.

## Verify

Ask Codex:

```
use skill tool to list skills
```

You should see `testmaster/unit_test` and `testmaster/e2e_test`.

## Usage

```
use skill tool to load testmaster/unit_test
use skill tool to load testmaster/e2e_test
```

Or just ask: "Generate unit tests for this project" — if the skill is discovered, Codex will use it.

## Update

```bash
cd ~/.codex/testmaster && git pull
```

Skills update instantly through the symlink.

## Uninstall

```bash
rm -rf ~/.agents/skills/testmaster
rm -rf ~/.codex/testmaster
```
