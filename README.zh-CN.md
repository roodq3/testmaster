[English](README.md) | [中文](README.zh-CN.md)

# TestMaster

一个 Claude Code 插件，PRD 驱动的测试生成。测试用例从需求设计，经过审查后再实现，按模块调度子代理执行。

## 功能特点

- **PRD 驱动** — 测试用例从产品需求文档推导，而非从代码猜测
- **先设计后编码** — 测试用例先设计、审查通过后再写代码
- **审查门禁** — 影响分析审查和测试用例审查，在实现前拦截问题
- **子代理调度** — 每个模块分配独立子代理，上下文干净，避免上下文污染
- **智能 Mock 边界** — 识别外部依赖，旁路依赖自动 Mock，其余让你决定
- **两种失败模式** — 测试代码 bug 修复测试；业务代码 bug 记录发现，绝不修改业务代码
- **增量更新** — 后续运行只重新分析和测试受影响的模块
- **中断恢复** — 进度保存到计划文件和测试用例文档，中断后从断点继续
- **框架无关** — 支持任何语言/框架

## 技能

| 命令 | 说明 |
|------|------|
| `/testmaster:unit-test` | PRD 驱动的单元测试，带审查门禁，按模块调度子代理 |
| `/testmaster:prd-keeper` | 需求新增或变更时保持 PRD 同步更新，生成结构化变更记录并携带 CLG 标签提交 |

## 安装

> 安装方式因平台而异。Claude Code 和 Cursor 有内置插件系统，Codex 和 OpenCode 需要手动设置。

### Claude Code

```bash
# 第一步：注册插件商店
/plugin marketplace add roodq3/testmaster

# 第二步：安装插件
/plugin install testmaster@testmaster
```

### Cursor

在 Cursor Agent 聊天中输入：

```
/plugin-add testmaster
```

### Codex

在 Codex 会话中粘贴：

```
Fetch and follow instructions from https://raw.githubusercontent.com/roodq3/testmaster/refs/heads/main/.codex/INSTALL.md
```

或手动安装：

```bash
git clone https://github.com/roodq3/testmaster.git ~/.codex/testmaster
mkdir -p ~/.agents/skills
ln -s ~/.codex/testmaster/skills ~/.agents/skills/testmaster
```

### OpenCode

在 OpenCode 会话中粘贴：

```
Fetch and follow instructions from https://raw.githubusercontent.com/roodq3/testmaster/refs/heads/main/.opencode/INSTALL.md
```

或手动安装：

```bash
git clone https://github.com/roodq3/testmaster.git ~/.config/opencode/testmaster
mkdir -p ~/.config/opencode/skills
ln -s ~/.config/opencode/testmaster/skills ~/.config/opencode/skills/testmaster
```

### 更新

| 平台 | 命令 |
|------|------|
| Claude Code | `/plugin update testmaster` |
| Cursor | `/plugin-update testmaster` |
| Codex | `cd ~/.codex/testmaster && git pull` |
| OpenCode | `cd ~/.config/opencode/testmaster && git pull` |

### 卸载

| 平台 | 命令 |
|------|------|
| Claude Code | `/plugin uninstall testmaster` |
| Cursor | `/plugin-remove testmaster` |
| Codex | `rm -rf ~/.agents/skills/testmaster ~/.codex/testmaster` |
| OpenCode | `rm -rf ~/.config/opencode/skills/testmaster ~/.config/opencode/testmaster` |

## 工作原理

### 前置条件

- 项目中必须存在 `docs/prd.md`（`prd-keeper` 在开发过程中自动创建和维护）

### PRD Keeper 流程

每次代码变更触发同步：

1. 分类变更范围（微调 / 修改 / 新增 / 删除 / 重构）
2. 原地更新 `docs/prd.md` 并递增版本号
3. 在 `docs/prd-changelog.md` 中追加变更记录，分配顺序编号（`CLG-001`、`CLG-002`、...）
4. 执行 `git diff --name-only`，将实际变更文件填入变更记录的**影响范围**字段
5. 代码 + PRD + 变更记录一起提交，commit message 带 `CLG: <编号>` 标签

#### 结构化变更记录

`docs/prd-changelog.md` 中每条记录有唯一的 `CLG-XXX` 编号，包含：

- **变更内容** — 一两句话描述
- **变更原因** — 为什么做这个变更
- **影响范围** — 受影响的文件（来自 `git diff`，非猜测）
- **迁移说明** — 数据库迁移、数据格式变更等

#### CLG 标签提交

prd-keeper 的提交在 commit message 中包含 `CLG: <编号>` 标签，关联到对应的变更记录：

```
feat(auth): add OAuth2 login flow

CLG: 003
```

这实现了可追溯性 — `git log --grep="CLG: 003"` 可以找到与某条变更记录相关的所有提交。

### 单元测试流程

```
读取 PRD + changelog → 影响分析 → 影响审查（门禁）
        ↓
框架搭建（如果需要）
        ↓
测试计划 + 用例设计（按模块）→ 用例审查（门禁）
        ↓
写计划文件 → 调度实现子代理（按模块）
        ↓
全量运行 → 覆盖率检查 → 输出总结
```

1. 读取 PRD 和 changelog，理解需求和最近的变更
2. 扫描代码库，映射模块和依赖关系，生成受影响模块列表
3. 影响审查子代理验证受影响列表（首次运行跳过，所有模块都是新的）
4. 如果尚未搭建测试框架，自动搭建
5. 按模块调度设计子代理，创建测试用例文档（`docs/testcase/`）
6. 用例审查子代理验证覆盖度和 Mock 策略
7. 按模块调度实现子代理——编写测试、运行、修复循环（最多 5 轮）
8. 运行完整测试套件，检查覆盖率，输出总结

### 源码修复边界

测试失败时，TestMaster 遵循严格规则：

- **测试代码 bug**（Mock 错误、断言错误）→ 修复测试
- **业务代码 bug**（真实缺陷）→ 记录发现，绝不修改业务代码
- **无法判断** → 视为业务代码 bug，标记待人工审查

### 中断恢复

所有进度保存到 `docs/plans/YYYY-MM-DD-HHmmss-unit-test.md` 和 `docs/testcase/`。Session 中断后：

- TestMaster 找到最新的计划文件和测试用例文档
- 定位第一个未完成的模块
- 从断点继续执行

## 贡献

开 Issue 并附上：框架名、源码文件、出了什么问题。

## 许可证

MIT
