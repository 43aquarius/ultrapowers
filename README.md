# Ultrapowers

> **先有证据，再有断言。先写测试，再写代码。先找根因，再提修复。**

Ultrapowers 是一个面向 AI 编程助手的全生命周期技能集。它将软件开发中反复验证的工作模式系统化为一组**可路由、可组合**的子技能，覆盖从会话启动到代码交付的完整链路。

---

## 快速开始

### 前置条件

本技能设计用于支持 [Agent Skills](https://docs.anthropic.com/en/docs/agents-and-tools/agent-skills/overview) 的 AI 编程环境：

- **Claude Code**（推荐）：使用 `Skill` 工具调用
- **Copilot CLI**：使用 `skill` 工具调用
- **Gemini CLI**：使用 `activate_skill` 工具激活

### 安装

将本目录放置于你的技能目录中：

```
# 项目级安装（仅当前项目可用）
cp -r ultrapowers/ .claude/skills/ultrapowers/

# 用户级安装（所有项目可用）
cp -r ultrapowers/ ~/.claude/skills/ultrapowers/
```

### 使用方式

技能采用**分层路由**设计。会话开始后，入口路由器（`SKILL.md`）根据当前任务类型自动分发到对应的子模块：

```
1. 判断任务类型 → 2. 读取对应子模块 → 3. 按需加载引用文档
```

你也可以直接指定使用某项技能，例如："用 TDD 实现这个功能"会自动路由到 `modules/red-green-cycle.md`。

---

## 八大层级 · 18 项技能

| 层级       | 技能                 | 一句话说明                                        |
| ---------- | -------------------- | ------------------------------------------------- |
| **启动层** | Skill Bootstrapping  | 会话开始时建立规则、优先级和安全防护              |
| **创意层** | Ideation             | 动手写代码前，先通过协作对话完成设计并获得批准    |
| **规划层** | Task Decomposition   | 将设计文档分解为有序、可执行的实施计划            |
| **执行层** | Delegate & Verify    | 为每个任务派发独立子代理，两阶段审查（规范+质量） |
|            | Plan Execution       | 在独立会话中从头到尾执行完整实施计划              |
| **质量层** | Red-Green Cycle      | 测试驱动开发：红→绿→重构，没看到失败就不写代码    |
|            | Root-Cause Analysis  | 系统化调试：4 阶段流程，绝不修复症状              |
|            | Code Review Request  | 派发独立审查代理，聚焦工作成果而非思维过程        |
|            | Review Feedback      | 收到审查反馈后，逐项理解、分类、回应              |
|            | Pre-Completion Check | 声称完成前的最终验证清单                          |
|            | Metric-Driven Dev    | 用可量化的指标验证正确性，而非主观判断            |
| **集成层** | Branch Isolation     | 用 Git Worktree 创建隔离工作区                    |
|            | Branch Wrap-Up       | 验证完整性、清理、合并、更新文档                  |
| **优化层** | Parallel Dispatch    | 多独立任务并发执行，每个域一个代理                |
|            | Budget Awareness     | LLM API 成本管控：模型路由、预算追踪、提示缓存    |
| **知识层** | Knowledge Capture    | 从会话中提取原子"本能"，演化为可复用技能          |
|            | Skill Authoring      | 遵循标准化规范创建和编辑技能                      |
|            | Skill Discovery      | 基于仓库证据将技能分类为 DAILY / LIBRARY          |

## 标准工作流

```
会话启动 → 需求构思 → 任务分解 → 分支隔离
    → [TDD实现 → 根因调试 → 代码审查 → 反馈处理 → 完成验证]（循环）
    → 分支收尾 → 知识沉淀
```

详细工作流说明见 [docs/ultrapowers-guide.md](docs/ultrapowers-guide.md)。

## 文件结构

```
ultrapowers/
├── SKILL.md                    ← 入口路由器
├── modules/                     ← 18 个子技能核心指令（~5000 行）
├── references/                  ← 参考文档（多平台工具、最佳实践）
├── prompts/                     ← 6 个子代理提示模板
├── scripts/                     ← 4 个辅助脚本
├── docs/                        ← 12 篇深度技术文档 + 使用指南
└── assets/                      ← 静态资源模板
```

## 设计原则

### 1. 防弹化（Bulletproofing）

每个技能使用"始终/绝不"而非"应该/尽量"。这不仅是风格选择——它旨在抵抗时间压力下的合理化倾向。当 AI 想说"就这一次跳过 TDD"时，铁律会阻止它。

### 2. 反模式驱动

每个技能明确列出"不要做什么"。例如：

| 危险信号           | 现实                                 |
| ------------------ | ------------------------------------ |
| "这只是个简单问题" | 问题就是任务。检查技能。             |
| "让我先探索代码库" | 技能告诉你**如何**探索。先检查。     |
| "我记得这个技能"   | 技能会演进。阅读当前版本。           |
| "这感觉很高效"     | 无纪律的行动浪费时间。技能防止这个。 |

### 3. 上下文预算意识

每个子模块保持精简。技能注册表让 AI 只加载当前任务需要的模块，避免一次性消耗大量上下文窗口。

### 4. 说服力原则

技能设计基于 [Meincke et al., 2025](https://arxiv.org/abs/2506.17553) 的研究——命令式语言、不可协商的框架和消除决策疲劳使 AI 合规率从 33% 提升至 72%。这不是操纵，而是确保关键实践即使在压力下也能被遵循。

## 指令优先级

```
用户指令（CLAUDE.md / 直接请求）> Ultrapowers 技能 > 系统默认行为
```

用户始终掌控一切。

## 文档索引

| 文档                                                                             | 内容                                           |
| -------------------------------------------------------------------------------- | ---------------------------------------------- |
| [SKILL.md](SKILL.md)                                                             | 入口路由器、技能注册表、标准工作流             |
| [docs/ultrapowers-guide.md](docs/ultrapowers-guide.md)                           | 详细使用指南（架构、每项技能详解、快速参考表） |
| [docs/CREATION-LOG.md](docs/CREATION-LOG.md)                                     | 技能创建日志与测试验证记录                     |
| [references/anthropic-best-practices.md](references/anthropic-best-practices.md) | Anthropic API 技能编写最佳实践                 |
| [references/persuasion-principles.md](references/persuasion-principles.md)       | 技能设计的说服力原则                           |
| [references/graphviz-conventions.md](references/graphviz-conventions.md)         | 流程图视觉规范（用 DOT 自身描述）              |

## 规模统计

| 类别           | 数量           |
| -------------- | -------------- |
| 子技能模块     | 18 个          |
| 子代理提示模板 | 6 个           |
| 参考文档       | 9 个（含示例） |
| 辅助脚本       | 4 个           |
| 深度技术文档   | 12 篇          |
| 总代码量       | ~8500 行       |

## 许可

本技能集作为参赛作品提交。内部技能内容源自开源 Superpowers 项目，遵循原项目许可。

---
