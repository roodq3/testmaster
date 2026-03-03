[English](README.md) | [中文](README.zh-CN.md)

# TestMaster

一个 Claude Code 插件，自动生成、运行、修复项目测试。

## 功能特点

- **自动识别技术栈** — 扫描项目，自动选择正确的测试框架
- **写到通过为止** — 生成测试后立即运行，失败则进入修复循环（最多 5 轮），直到通过
- **增量生成** — 只为新增/未覆盖的代码编写测试，不重复已有测试
- **全量回归检查** — 每次都运行完整测试套件，捕获新代码引入的回归问题
- **覆盖率门槛** — 检查行覆盖率（≥ 80%）和分支覆盖率（≥ 70%），不达标自动补测试
- **智能 Mock 边界** — 旁路依赖自动 Mock，其余依赖列清单让你决定
- **中断恢复** — 进度保存到磁盘上的计划文件，中断后从断点继续
- **框架无关** — 支持任何语言/框架；内置参考文件为主流技术栈提供额外质量保障
- **E2E 补充模式** — 运行 `/testmaster:e2e_test <流程描述>` 可单独补充指定流程，无需重新扫描

## 技能

| 命令 | 说明 |
|------|------|
| `/testmaster:unit_test` | 逐文件生成单元测试，带覆盖率检查 |
| `/testmaster:e2e_test` | 识别用户流程，使用 Page Object Model 编写端到端测试 |
| `/testmaster:e2e_test <描述>` | 在已有 E2E 测试基础上补充指定流程 |

## 内置参考文件

针对以下框架提供最佳实践和常见陷阱：

| 单元测试 | E2E 测试 |
|----------|----------|
| Spring Boot (JUnit5 + Mockito) | Playwright |
| Vue (Vitest + Vue Test Utils) | Cypress |
| React (Vitest/Jest + Testing Library) | Spring Boot 集成测试 |
| TypeScript (Vitest) | Python (FastAPI / Django / Flask) |
| Python (pytest) | |
| 原生 H5 (Vitest + jsdom) | |

你的技术栈不在列表中？**TestMaster 仍然可用。** 参考文件是可选增强，帮助 Claude 避免框架特定的陷阱。欢迎 PR 添加更多框架。

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

### 单元测试流程

```
检测技术栈 → 检查基础设施 → 扫描 & 生成计划 → 逐文件编写 → 逐文件运行 → 修复循环
                                                                       ↓
                                                       全量运行 → 覆盖率检查 → 输出总结
```

1. 检测技术栈，读取匹配的参考文件（如果有）
2. 确认测试框架已安装，缺失则安装
3. 扫描所有源码文件，跳过已有测试的，生成计划文件
4. 对每个文件：编写测试 → 只运行该文件的测试 → 修复循环（最多 5 轮）
5. 运行完整测试套件，检查覆盖率，在终端输出总结

### E2E 测试流程

```
检测技术栈 → 识别流程 → 评估 Mock 边界 → 配置环境 → 逐流程编写 → 修复循环
                              ↓                              ↓
                       询问用户确认                   全量运行 → 生成报告
```

1. 检测技术栈和 E2E 框架（前端项目无框架时默认安装 Playwright）
2. 按优先级识别用户流程（P0 认证/核心 → P1 CRUD/搜索 → P2 边缘场景）
3. 列出所有外部依赖——旁路依赖自动 Mock，其余询问你：Mock 还是真实对接
4. 选择真实对接的依赖：展示检测到的配置项，让你确认配置正确
5. 逐流程编写测试，使用 Page Object Model，复用已有 Page Object
6. 全量运行，生成 Markdown 报告

### 中断恢复

所有进度保存到 `docs/plans/YYYY-MM-DD-HHmmss-*.md`。Session 中断后：

- TestMaster 找到最新的计划文件
- 定位第一个未完成的任务
- 从断点继续执行

### 源码修复边界

测试失败时，TestMaster 按以下规则处理：

- **测试写法有误** → 修复测试
- **源码明显 bug**（null 未处理、数组越界）→ 修复源码并在计划文件中记录
- **设计问题或不确定** → 不改源码，标记待你确认

## 贡献

### 添加参考文件

在 `skills/unit_test/references/` 或 `skills/e2e_test/references/` 下创建文件：

- 只写 Claude 容易犯错的内容——陷阱、Mock 策略、命名规范
- 不要写代码示例、安装命令、配置模板
- 控制在 40 行以内
- 参考已有文件的格式

### 反馈问题

开 Issue 并附上：框架名、源码文件、出了什么问题。

## 许可证

MIT
