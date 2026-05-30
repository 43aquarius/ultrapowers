---
name: ultrapowers
description: "Use when starting any conversation (session bootstrap), creating features or building components (ideation/TDD), executing implementation plans (delegate-and-verify, plan-execution), debugging bugs or unexpected behavior (root-cause-analysis), requesting or receiving code review (code-review-request, review-feedback), verifying work before completion (pre-completion-check, metric-driven-dev), managing git branches (branch-isolation, branch-wrap-up), dispatching parallel agents (parallel-dispatch), optimizing LLM API costs (budget-awareness), capturing reusable knowledge (knowledge-capture), or authoring/discovering skills (skill-authoring, skill-discovery)."
---

# Ultrapowers — 单体技能入口

本技能合并了 18 个子技能，覆盖从会话启动到代码交付的完整开发生命周期。

**核心原则：** 先有证据再有断言。先写测试再写代码。先找根因再提修复。

## 如何使用

本技能采用**分层路由**设计。根据当前任务类型，读取对应的子模块文件获取详细指令。

**路由规则：**
1. 判断当前任务属于哪个层级（见下方注册表）
2. 读取 `modules/<skill-name>.md` 获取该技能的完整指令
3. 如果子模块引用了 `../references/`、`../prompts/`、`../scripts/` 或 `../docs/` 中的文件，按需读取

## 技能注册表

### 启动层

| 技能 | 文件 | 触发条件 |
|------|------|---------|
| skill-bootstrapping | `modules/skill-bootstrapping.md` | 每次会话开始 — 建立技能查找和使用机制 |

### 创意层

| 技能 | 文件 | 触发条件 |
|------|------|---------|
| ideation | `modules/ideation.md` | 创建功能、构建组件、添加功能或修改行为之前 |

### 规划层

| 技能 | 文件 | 触发条件 |
|------|------|---------|
| task-decomposition | `modules/task-decomposition.md` | 有规范或多步骤任务需要制定实施计划 |

### 执行层

| 技能 | 文件 | 触发条件 |
|------|------|---------|
| delegate-and-verify | `modules/delegate-and-verify.md` | 在当前会话中执行具有独立任务的实施计划 |
| plan-execution | `modules/plan-execution.md` | 在单独会话中执行书面实施计划 |

### 质量层

| 技能 | 文件 | 触发条件 |
|------|------|---------|
| red-green-cycle | `modules/red-green-cycle.md` | 实现任何功能或 bug 修复，在编写实现代码之前 |
| root-cause-analysis | `modules/root-cause-analysis.md` | 遇到 bug、测试失败或意外行为，在提出修复之前 |
| code-review-request | `modules/code-review-request.md` | 完成任务、实现主要功能或在合并前验证工作 |
| review-feedback | `modules/review-feedback.md` | 收到代码审查反馈，在实施建议之前 |
| pre-completion-check | `modules/pre-completion-check.md` | 即将声称工作完成、修复或通过时 |
| metric-driven-dev | `modules/metric-driven-dev.md` | 实现可测量正确性的功能或修复 bug |

### 集成层

| 技能 | 文件 | 触发条件 |
|------|------|---------|
| branch-isolation | `modules/branch-isolation.md` | 开始需要隔离的功能工作或在执行计划之前 |
| branch-wrap-up | `modules/branch-wrap-up.md` | 实现完成、所有测试通过，需要集成工作时 |

### 优化层

| 技能 | 文件 | 触发条件 |
|------|------|---------|
| parallel-dispatch | `modules/parallel-dispatch.md` | 面临 2 个以上独立且无共享状态的任务 |
| budget-awareness | `modules/budget-awareness.md` | 构建调用 LLM API 的应用或需要控制 API 支出 |

### 知识层

| 技能 | 文件 | 触发条件 |
|------|------|---------|
| knowledge-capture | `modules/knowledge-capture.md` | 会话结束时或发现可重用模式时 |
| skill-authoring | `modules/skill-authoring.md` | 创建新技能、编辑现有技能或验证技能有效性 |
| skill-discovery | `modules/skill-discovery.md` | 新项目设置、审计已安装技能或减少上下文膨胀 |

## 标准工作流路由

典型开发流程按以下顺序触发技能：

```
1. skill-bootstrapping    → 会话启动，建立规则
2. ideation               → 理解需求，设计方案
3. task-decomposition     → 制定实施计划
4. branch-isolation       → 创建隔离工作区
5. delegate-and-verify    → 逐任务执行（循环）
   └─ 每个任务内：
      a. red-green-cycle       → TDD 实现
      b. root-cause-analysis   → 如遇 bug
      c. code-review-request   → 任务完成后审查
      d. review-feedback       → 处理审查反馈
      e. pre-completion-check   → 验证完成
6. metric-driven-dev      → 回归验证（可选）
7. branch-wrap-up         → 完成开发分支
8. knowledge-capture      → 会话结束，提取知识
```

## 文件目录

```
ultrapowers/
├── SKILL.md                    ← 本文件（入口路由器）
├── modules/                     ← 18 个子技能核心内容
│   ├── skill-bootstrapping.md
│   ├── ideation.md
│   ├── task-decomposition.md
│   ├── delegate-and-verify.md
│   ├── plan-execution.md
│   ├── branch-isolation.md
│   ├── branch-wrap-up.md
│   ├── red-green-cycle.md
│   ├── root-cause-analysis.md
│   ├── code-review-request.md
│   ├── review-feedback.md
│   ├── pre-completion-check.md
│   ├── metric-driven-dev.md
│   ├── parallel-dispatch.md
│   ├── budget-awareness.md
│   ├── knowledge-capture.md
│   ├── skill-authoring.md
│   └── skill-discovery.md
├── references/                  ← 引用文档（API 参考、最佳实践等）
│   ├── codex-tools.md
│   ├── copilot-tools.md
│   ├── gemini-tools.md
│   ├── anthropic-best-practices.md
│   ├── persuasion-principles.md
│   ├── graphviz-conventions.dot
│   ├── testing-skills-with-subagents.md
│   ├── render-graphs.js
│   └── examples/
│       └── CLAUDE_MD_TESTING.md
├── prompts/                     ← 子代理提示模板
│   ├── code-reviewer.md
│   ├── implementer-prompt.md
│   ├── spec-reviewer-prompt.md
│   ├── code-quality-reviewer-prompt.md
│   ├── spec-document-reviewer-prompt.md
│   └── plan-document-reviewer-prompt.md
├── scripts/                     ← 可执行脚本
│   ├── helper.js
│   ├── start-server.sh
│   ├── stop-server.sh
│   └── find-polluter.sh
├── docs/                        ← 长篇参考文档
│   ├── visual-companion.md
│   ├── condition-based-waiting.md
│   ├── condition-based-waiting-example.ts
│   ├── root-cause-tracing.md
│   ├── defense-in-depth.md
│   ├── test-academic.md
│   ├── test-pressure-1.md
│   ├── test-pressure-2.md
│   ├── test-pressure-3.md
│   ├── testing-anti-patterns.md
│   └── CREATION-LOG.md
└── assets/                      ← 静态资源
    └── frame-template.html
```

## 指令优先级

1. **用户的明确指令**（CLAUDE.md、直接请求）— 最高优先级
2. **本技能及子模块** — 覆盖默认系统行为
3. **默认系统提示** — 最低优先级
