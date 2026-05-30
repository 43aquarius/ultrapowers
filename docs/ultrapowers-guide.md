# Ultrapowers 详细使用指南

## 目录

1. [概述](#概述)
2. [核心理念](#核心理念)
3. [架构设计](#架构设计)
4. [八大层级与 18 项技能详解](#八大层级与-18-项技能详解)
5. [标准工作流](#标准工作流)
6. [文件目录结构](#文件目录结构)
7. [指令优先级](#指令优先级)
8. [快速参考表](#快速参考表)

---

## 概述

**Ultrapowers** 是一个面向 AI 编程助手的全生命周期技能集。它将软件开发过程中反复验证过的工作模式系统化为一组**可路由、可组合**的子技能，覆盖从会话启动到代码交付的完整链路。

本技能合并了 **18 个子技能模块**，配备 **6 个子代理提示模板**、**9 个参考文档**、**4 个辅助脚本**、**12 篇深度技术文档**，以及 **1 个静态资源模板**。

### 适用场景

- 使用 AI 助手（Claude Code、Copilot CLI、Gemini CLI 等）进行软件开发
- 需要系统化的 TDD、调试、代码审查工作流
- 需要并行任务调度与 LLM API 成本管控
- 需要在多会话间沉淀和复用开发知识

---

## 核心理念

> **先有证据，再有断言。先写测试，再写代码。先找根因，再提修复。**

这不是一句口号——它被编码为每个子技能中的"铁律"和"反模式"。

### 三条铁律

| 铁律 | 含义 | 对应技能 |
|------|------|---------|
| **证据优先** | 没有失败的测试就不写生产代码；没有根因调查就不提修复 | red-green-cycle、root-cause-analysis |
| **隔离执行** | 每个任务用独立子代理，精确构建上下文，不继承会话历史 | delegate-and-verify、parallel-dispatch |
| **完成验证** | 声称完成之前必须通过独立验证，不接受自我评估 | pre-completion-check、metric-driven-dev |

### 设计哲学

1. **防弹化**：使用"始终/绝不"而非"应该/尽量"，抵抗时间压力下的合理化倾向
2. **反模式驱动**：每个技能都明确列出"不要做什么"，而不仅是"要做什么"
3. **分层路由**：技能入口根据任务类型自动分发到对应子模块，避免一次性加载全部内容
4. **上下文预算意识**：每个子模块保持精简，避免无谓消耗上下文窗口

---

## 架构设计

### 分层路由模型

Ultrapowers 采用**分层路由**设计。`SKILL.md` 是唯一的入口路由器，根据当前任务类型将执行分发到 `modules/` 下的对应子模块。

```
用户请求
    │
    ▼
┌──────────────────┐
│   SKILL.md       │  ← 入口路由器：判断任务类型
│  (技能注册表)     │
└──────┬───────────┘
       │
       ├── 启动层 ──→ modules/skill-bootstrapping.md
       ├── 创意层 ──→ modules/ideation.md
       ├── 规划层 ──→ modules/task-decomposition.md
       ├── 执行层 ──→ modules/delegate-and-verify.md / plan-execution.md
       ├── 质量层 ──→ modules/red-green-cycle.md / root-cause-analysis.md / ...
       ├── 集成层 ──→ modules/branch-isolation.md / branch-wrap-up.md
       ├── 优化层 ──→ modules/parallel-dispatch.md / budget-awareness.md
       └── 知识层 ──→ modules/knowledge-capture.md / skill-authoring.md / skill-discovery.md
```

### 路由规则

1. 判断当前任务属于哪个层级（见技能注册表）
2. 读取 `modules/<skill-name>.md` 获取该技能的完整指令
3. 如果子模块引用了 `references/`、`prompts/`、`scripts/` 或 `docs/` 中的文件，按需读取

---

## 八大层级与 18 项技能详解

### 一、启动层

#### skill-bootstrapping — 会话启动

**文件**: `modules/skill-bootstrapping.md`

**触发时机**: 每次会话开始

**核心职责**:
- 建立技能查找和使用机制
- 设置指令优先级（用户指令 > 技能 > 系统默认）
- 配置平台适配（Claude Code / Copilot CLI / Gemini CLI）
- 启用安全防护模式（谨慎模式、冻结模式、守护模式）
- 建立上下文预算意识

**关键设计**:
- 子代理执行特定任务时自动跳过此技能（`<SUBAGENT-STOP>` 标记）
- 即使只有 1% 的概率某个技能适用，也必须调用检查
- 列出 12 种"危险信号"——当 AI 产生这些想法时意味着在找借口

---

### 二、创意层

#### ideation — 头脑风暴

**文件**: `modules/ideation.md`

**触发时机**: 创建功能、构建组件、添加功能或修改行为之前

**核心职责**:
- 通过协作对话将想法转化为完整设计和规范
- 探索项目背景 → 提出澄清问题 → 提供 2-3 种方案 → 展示设计 → 获得批准

**铁律**:
```
在展示设计并获得用户批准之前，不得调用任何实现技能、编写任何代码、搭建任何项目或采取任何实现措施。
```

**9 步清单**:
1. 探索项目背景（检查文件、文档、最近提交）
2. 提供可视化伴侣（如涉及视觉问题）
3. 提出澄清问题（一次一个）
4. 提出 2-3 种方案（附带权衡和推荐）
5. 展示设计（按复杂度分段，每段获取批准）
6. 编写设计文档（保存到 `docs/ultrapowers/specs/`）
7. 规范自查（占位符、矛盾、歧义、范围）
8. 用户审阅书面规范
9. 过渡到实现（调用任务分解技能）

---

### 三、规划层

#### task-decomposition — 任务分解

**文件**: `modules/task-decomposition.md`

**触发时机**: 有规范或多步骤任务需要制定实施计划

**核心职责**:
- 将设计文档分解为有序的实施计划
- 每个任务具备：目标、文件清单、依赖关系、验收标准
- 输出结构化的计划文档

---

### 四、执行层

#### delegate-and-verify — 子代理驱动开发

**文件**: `modules/delegate-and-verify.md`

**触发时机**: 在当前会话中执行具有独立任务的实施计划

**核心模式**:
- 为每个任务派发新的子代理
- 每个任务后进行**两阶段审查**：规范符合性审查 → 代码质量审查
- 子代理获得精确构建的上下文，不继承会话历史
- 持续执行，不在任务之间停下来签到

#### plan-execution — 计划执行

**文件**: `modules/plan-execution.md`

**触发时机**: 在单独会话中执行书面实施计划

**与 delegate-and-verify 的区别**:
- `delegate-and-verify`：在当前会话中，用子代理逐个执行
- `plan-execution`：在独立会话中，从头到尾执行完整计划

---

### 五、质量层（6 项技能）

#### red-green-cycle — 测试驱动开发（TDD）

**文件**: `modules/red-green-cycle.md`

**铁律**:
```
没有失败的测试，不允许写生产代码
```

**核心流程**: 红（写测试，看它失败）→ 绿（写最简代码让它通过）→ 重构

**永远适用的场景**: 新功能、Bug 修复、重构、行为更改
**例外（需询问）**: 一次性原型、生成的代码、配置文件

#### root-cause-analysis — 系统化调试

**文件**: `modules/root-cause-analysis.md`

**铁律**:
```
没有根本原因调查，不允许修复
```

**4 阶段流程**:
1. **调查** — 收集证据，复现问题，记录观察
2. **模式分析** — 寻找规律，缩小范围
3. **假设** — 形成单一假设（不允许散弹式猜测）
4. **实施** — 修复并验证

**防弹设计**: 使用"始终/绝不"语言，明确"如果你的第一次修复无效"的强制操作

#### code-review-request — 请求代码审查

**文件**: `modules/code-review-request.md`

**触发时机**: 完成任务、实现主要功能、合并前验证

**核心设计**: 派发代码审查代理，提供精确构建的上下文——**不是会话历史**，而是工作成果本身。这使审查者专注于代码而非思维过程。

#### review-feedback — 代码审查接收

**文件**: `modules/review-feedback.md`

**触发时机**: 收到代码审查反馈后，实施建议之前

**核心职责**:
- 理解审查意见的技术依据
- 区分"必须修复"与"建议改进"
- 在不理解时请求澄清
- 逐项回应每个审查点

#### pre-completion-check — 完成前验证

**文件**: `modules/pre-completion-check.md`

**触发时机**: 即将声称工作完成、修复或通过时

**验证清单**:
- 所有测试通过？
- 没有调试代码残留？
- 没有 TODO/FIXME 占位符？
- 代码风格一致？
- 边界情况已处理？

#### metric-driven-dev — 度量驱动开发

**文件**: `modules/metric-driven-dev.md`

**触发时机**: 实现可测量正确性的功能或修复 bug

**核心方法**: 定义可量化的成功标准，通过自动化测试验证度量指标，而非依赖主观判断。

---

### 六、集成层

#### branch-isolation — 分支隔离

**文件**: `modules/branch-isolation.md`

**触发时机**: 开始需要隔离的功能工作或在执行计划之前

**核心职责**: 使用 Git Worktree 创建隔离的工作环境，避免污染主分支

#### branch-wrap-up — 分支收尾

**文件**: `modules/branch-wrap-up.md`

**触发时机**: 实现完成、所有测试通过，需要集成工作时

**核心职责**: 验证完整性、清理工作区、合并分支、更新文档

---

### 七、优化层

#### parallel-dispatch — 并行派发代理

**文件**: `modules/parallel-dispatch.md`

**触发时机**: 面临 2 个以上独立且无共享状态的任务

**核心原则**: 每个独立问题域派发一个代理，让它们并发工作

**使用场景**:
- 3 个以上测试文件因不同根因而失败
- 多个子系统独立损坏
- 调查之间没有共享状态

**真实案例**: 重大重构后 3 个文件 6 个测试失败 → 并行派发 3 个代理 → 所有调查同时完成 → 零冲突集成

**去草率化模式**: 实现完成后，用专门的清理代理移除冗余测试、类型检查、console.log 等

#### budget-awareness — 成本感知开发

**文件**: `modules/budget-awareness.md`

**触发时机**: 构建调用 LLM API 的应用或需要控制 API 支出

**四种技术**:

| 技术 | 说明 |
|------|------|
| 模型路由 | 按复杂度选择模型：简单→Haiku，中等→Sonnet，复杂→Opus |
| 成本追踪 | 使用不可变数据类跟踪累计支出 |
| 窄范围重试 | 仅重试暂时性错误，认证/请求错误立即失败 |
| 提示缓存 | 对超过 1024 令牌的系统提示词使用 ephemeral 缓存 |

**价格参考（2025-2026）**:

| 模型 | 输入 | 输出 | 相对成本 |
|------|------|------|---------|
| Haiku 4.5 | $0.80/M | $4.00/M | 1x |
| Sonnet 4.6 | $3.00/M | $15.00/M | ~4x |
| Opus 4.5 | $15.00/M | $75.00/M | ~19x |

---

### 八、知识层

#### knowledge-capture — 持续学习

**文件**: `modules/knowledge-capture.md`

**触发时机**: 会话结束时或发现可重用模式时

**学习管道**: 会话 → 本能（原子观察）→ 集群（相关分组）→ 技能/命令/代理（晋升）

**三层结构**:

| 层级 | 说明 | 晋升标准 |
|------|------|---------|
| 本能 | 单个原子观察，带置信度评分 | — |
| 集群 | 3+ 相关本能分组 | — |
| 技能 | 完整可重用技能 | 2+ 项目出现，置信度 ≥ 0.8 |

**置信度评分**: 0.3（单次观察）→ 0.5（两次/用户确认）→ 0.7（多会话）→ 0.9（多项目确认）

#### skill-authoring — 编写技能

**文件**: `modules/skill-authoring.md`

**触发时机**: 创建新技能、编辑现有技能或验证技能有效性

**核心职责**: 遵循标准化的技能编写规范，包括触发条件、铁律、反模式、流程图、验证测试等要素

#### skill-discovery — 技能加载

**文件**: `modules/skill-discovery.md`

**触发时机**: 新项目设置、审计已安装技能或减少上下文膨胀

**两个分类**:

| 分类 | 含义 | 操作 |
|------|------|------|
| DAILY | 每个会话加载 | 保留在 `.claude/skills/` |
| LIBRARY | 可访问但不默认加载 | 保留在可搜索的参考位置 |

**核心原则**: 每个加载到每个会话的令牌都消耗上下文。只加载这个项目实际使用的内容。

---

## 标准工作流

典型开发流程按以下顺序触发技能：

```
┌─────────────────────────────────────────────────┐
│  1. skill-bootstrapping                          │
│     └─ 会话启动，建立规则和优先级                   │
├─────────────────────────────────────────────────┤
│  2. ideation                                     │
│     └─ 理解需求，设计方案，获得用户批准              │
├─────────────────────────────────────────────────┤
│  3. task-decomposition                           │
│     └─ 将设计分解为有序的实施计划                   │
├─────────────────────────────────────────────────┤
│  4. branch-isolation                             │
│     └─ 创建隔离的 Git Worktree                    │
├─────────────────────────────────────────────────┤
│  5. delegate-and-verify（循环执行每个任务）         │
│     ├─ a. red-green-cycle  → TDD 实现             │
│     ├─ b. root-cause-analysis → 如遇 bug          │
│     ├─ c. code-review-request → 任务完成后审查     │
│     ├─ d. review-feedback  → 处理审查反馈         │
│     └─ e. pre-completion-check → 验证完成         │
├─────────────────────────────────────────────────┤
│  6. metric-driven-dev（可选）                     │
│     └─ 回归验证，度量驱动确认                       │
├─────────────────────────────────────────────────┤
│  7. branch-wrap-up                               │
│     └─ 完成开发分支，集成到主分支                   │
├─────────────────────────────────────────────────┤
│  8. knowledge-capture                            │
│     └─ 会话结束，提取可重用知识                     │
└─────────────────────────────────────────────────┘
```

### 何时使用并行派发

在执行层的循环中，如果遇到多个独立的任务或失败，可以切换为 `parallel-dispatch` 模式：

```
多个独立任务/失败
       │
       ▼
parallel-dispatch
    ├─ 代理 1 → 任务/域 A
    ├─ 代理 2 → 任务/域 B        （并发执行）
    └─ 代理 3 → 任务/域 C
       │
       ▼
  审查集成（验证无冲突）
```

### 何时使用成本管控

在以下场景自动叠加 `budget-awareness`：

- 派发子代理时 → 按任务复杂度路由模型
- 批量处理时 → 设置预算上限
- 构建 LLM 应用时 → 实现提示缓存和重试逻辑

---

## 文件目录结构

```
ultrapowers/
├── SKILL.md                          ← 入口路由器（本技能的主文件）
│
├── modules/                           ← 18 个子技能核心指令
│   ├── skill-bootstrapping.md         │  启动层
│   ├── ideation.md                    │  创意层
│   ├── task-decomposition.md          │  规划层
│   ├── delegate-and-verify.md         │
│   ├── plan-execution.md              │  执行层
│   ├── branch-isolation.md            │
│   ├── branch-wrap-up.md              │  集成层
│   ├── red-green-cycle.md             │
│   ├── root-cause-analysis.md         │
│   ├── code-review-request.md         │
│   ├── review-feedback.md             │  质量层
│   ├── pre-completion-check.md        │
│   ├── metric-driven-dev.md           │
│   ├── parallel-dispatch.md           │
│   ├── budget-awareness.md            │  优化层
│   ├── knowledge-capture.md           │
│   ├── skill-authoring.md             │  知识层
│   └── skill-discovery.md             │
│
├── references/                        ← 参考文档
│   ├── anthropic-best-practices.md    │  Anthropic API 最佳实践
│   ├── codex-tools.md                 │  Codex 平台工具映射
│   ├── copilot-tools.md               │  Copilot CLI 工具映射
│   ├── gemini-tools.md                │  Gemini CLI 工具映射
│   ├── graphviz-conventions.md        │  流程图视觉规范
│   ├── persuasion-principles.md       │  说服力原则
│   ├── render-graphs.js               │  Graphviz 渲染脚本
│   ├── testing-skills-with-subagents.md│  子代理测试方法
│   └── examples/
│       └── CLAUDE_MD_TESTING.md       │  CLAUDE.md 测试示例
│
├── prompts/                           ← 子代理提示模板
│   ├── code-reviewer.md               │  代码审查代理提示
│   ├── code-quality-reviewer-prompt.md│  代码质量审查提示
│   ├── implementer-prompt.md          │  实现代理提示
│   ├── plan-document-reviewer-prompt.md│ 计划文档审查提示
│   ├── spec-document-reviewer-prompt.md│ 规格文档审查提示
│   └── spec-reviewer-prompt.md        │  规格审查提示
│
├── scripts/                           ← 可执行脚本
│   ├── helper.js                      │  通用辅助函数
│   ├── start-server.sh                │  启动开发服务器
│   ├── stop-server.sh                 │  停止开发服务器
│   └── find-polluter.sh               │  查找污染源脚本
│
├── docs/                              ← 长篇深度文档
│   ├── visual-companion.md            │  可视化伴侣指南
│   ├── condition-based-waiting.md     │  基于条件的等待模式
│   ├── condition-based-waiting-example.ts │  等待模式示例代码
│   ├── root-cause-tracing.md          │  根因追溯方法
│   ├── defense-in-depth.md            │  防御性编程
│   ├── test-academic.md               │  测试学术参考
│   ├── test-pressure-1.md             │  测试压力场景 1
│   ├── test-pressure-2.md             │  测试压力场景 2
│   ├── test-pressure-3.md             │  测试压力场景 3
│   ├── testing-anti-patterns.md       │  测试反模式
│   ├── CREATION-LOG.md                │  技能创建日志
│   └── ultrapowers-guide.md           │  本文件
│
└── assets/                            ← 静态资源
    └── frame-template.html            │  HTML 框架模板
```

---

## 指令优先级

当指令发生冲突时，遵循以下优先级：

| 优先级 | 来源 | 说明 |
|--------|------|------|
| **1（最高）** | 用户明确指令 | CLAUDE.md、GEMINI.md、AGENTS.md、直接请求 |
| **2** | Ultrapowers 技能及子模块 | 覆盖默认系统行为 |
| **3（最低）** | 默认系统提示 | 被技能和用户指令覆盖 |

**示例**: 如果 CLAUDE.md 说"不要使用 TDD"而技能说"始终使用 TDD"——遵循用户指令。用户掌控一切。

---

## 快速参考表

### 按场景查找技能

| 你想做什么 | 使用技能 | 文件 |
|-----------|---------|------|
| 开始新会话 | Skill Bootstrapping | `modules/skill-bootstrapping.md` |
| 理解需求、设计方案 | Ideation | `modules/ideation.md` |
| 将设计转为实施计划 | Task Decomposition | `modules/task-decomposition.md` |
| 在当前会话逐任务执行 | Delegate & Verify | `modules/delegate-and-verify.md` |
| 在独立会话执行完整计划 | Plan Execution | `modules/plan-execution.md` |
| 用 TDD 实现功能 | Red-Green Cycle | `modules/red-green-cycle.md` |
| 调试 bug | Root-Cause Analysis | `modules/root-cause-analysis.md` |
| 请求代码审查 | Code Review Request | `modules/code-review-request.md` |
| 处理审查反馈 | Review Feedback | `modules/review-feedback.md` |
| 验证完成状态 | Pre-Completion Check | `modules/pre-completion-check.md` |
| 度量驱动验证 | Metric-Driven Dev | `modules/metric-driven-dev.md` |
| 创建隔离工作区 | Branch Isolation | `modules/branch-isolation.md` |
| 完成开发分支 | Branch Wrap-Up | `modules/branch-wrap-up.md` |
| 并行处理多个任务 | Parallel Dispatch | `modules/parallel-dispatch.md` |
| 控制 API 成本 | Budget Awareness | `modules/budget-awareness.md` |
| 沉淀可复用知识 | Knowledge Capture | `modules/knowledge-capture.md` |
| 创建/编辑技能 | Skill Authoring | `modules/skill-authoring.md` |
| 管理技能加载 | Skill Discovery | `modules/skill-discovery.md` |

### 按层级查找技能

| 层级 | 技能数量 | 技能列表 |
|------|---------|---------|
| 启动层 | 1 | Skill Bootstrapping |
| 创意层 | 1 | Ideation |
| 规划层 | 1 | Task Decomposition |
| 执行层 | 2 | Delegate & Verify, Plan Execution |
| 质量层 | 6 | Red-Green Cycle, Root-Cause Analysis, Code Review Request, Review Feedback, Pre-Completion Check, Metric-Driven Dev |
| 集成层 | 2 | Branch Isolation, Branch Wrap-Up |
| 优化层 | 2 | Parallel Dispatch, Budget Awareness |
| 知识层 | 3 | Knowledge Capture, Skill Authoring, Skill Discovery |

### 子代理提示模板速查

| 模板 | 用途 | 文件 |
|------|------|------|
| 代码审查 | 派发代码审查代理 | `prompts/code-reviewer.md` |
| 代码质量审查 | 派发质量审查代理 | `prompts/code-quality-reviewer-prompt.md` |
| 实现代理 | 派发代码实现代理 | `prompts/implementer-prompt.md` |
| 规格审查 | 审查设计规格 | `prompts/spec-reviewer-prompt.md` |
| 规格文档审查 | 审查规格文档 | `prompts/spec-document-reviewer-prompt.md` |
| 计划文档审查 | 审查实施计划 | `prompts/plan-document-reviewer-prompt.md` |

---

## 上下文预算参考

| 资源类型 | 令牌成本 | 建议上限 |
|---------|---------|---------|
| 散文 | 单词数 × 1.3 | — |
| 代码文件 | 字符数 ÷ 4 | — |
| 代理描述 | 每个会话加载 | ≤ 30 词 |
| MCP 工具模式 | ~500 令牌/工具 | 谨慎添加 |
| 会话启动技能 | 每个技能 | ≤ 200 词 |
| CLAUDE.md 链 | 总行数 | ≤ 300 行 |
