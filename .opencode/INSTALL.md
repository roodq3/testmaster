# TestMaster — OpenCode Installation

Enable TestMaster skills in OpenCode via native skill discovery. Clone and symlink.

## Install

Clone the repository:

```bash
git clone https://github.com/roodq3/testmaster.git ~/.config/opencode/testmaster
```

Create a symlink so OpenCode discovers the skills:

```bash
mkdir -p ~/.config/opencode/skills
ln -s ~/.config/opencode/testmaster/skills ~/.config/opencode/skills/testmaster
```

Restart OpenCode to discover the skills.

## Verify

Ask OpenCode:

```
use skill tool to list skills
```

You should see `testmaster/unit_test` and `testmaster/e2e_test`.

## Usage

```
use skill tool to load testmaster/unit_test
use skill tool to load testmaster/e2e_test
```

Or just ask: "Generate unit tests for this project" — if the skill is discovered, OpenCode will use it.

## Update

```bash
cd ~/.config/opencode/testmaster && git pull
```

Skills update instantly through the symlink.

## Uninstall

```bash
rm -rf ~/.config/opencode/skills/testmaster
rm -rf ~/.config/opencode/testmaster
```
