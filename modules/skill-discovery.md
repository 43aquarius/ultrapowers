
# 技能加载

## 概述

基于仓库证据而非猜测，将技能分类为 DAILY（每个会话加载）vs LIBRARY（可访问但不默认加载）。

**核心原则：** 每个加载到每个会话的令牌都消耗上下文。只加载这个项目实际使用的内容。

## 何时使用

- 为新项目设置 ultrapowers
- 审计已安装技能的膨胀
- 减少无关技能造成的上下文开销
- 在添加许多技能、MCP 服务器或代理后

## 两个分类

| 分类 | 含义 | 操作 |
|------|------|------|
| **DAILY** | 为此项目每个会话加载 | 保留在 `.claude/skills/` |
| **LIBRARY** | 可访问但不默认加载 | 保留在可搜索的参考位置 |

## 证据来源

在分类任何内容之前，从仓库收集证据：

```bash
# 文件扩展名和结构
rg --files | head -50

# 包管理器和框架
cat package.json
cat pyproject.toml
cat Cargo.toml
cat go.mod

# CI 和工具配置
cat .github/workflows/*.yml
```

## 分类流程

### 1. 阅读仓库

确定实际技术栈：
- 使用的语言
- 框架和运行时
- 包管理器
- 测试技术栈
- 代码检查/格式化工具

### 2. 构建证据表

对于每个候选技能，记录：

```
skills/frontend-patterns | DAILY | 84 个 .tsx 文件，存在 next.config.ts | 核心前端技术栈
skills/django-patterns   | LIBRARY | 无 .py 文件，无 pyproject.toml       | 此仓库中未激活
skills/python-patterns   | LIBRARY | 零个 Python 源文件             | 仅保持可访问
```

### 3. 分类

**提升至 DAILY 当：**
- 仓库明确使用匹配的技术栈
- 技能足够通用，有助于每个会话
- 仓库已依赖相应的运行时或工作流

**降级至 LIBRARY 当：**
- 技能与技术栈不匹配
- 以后可能需要，但不是每天
- 增加上下文开销而无直接相关性

### 4. 验证

应用计划后：
- 每个 DAILY 技能存在于预期位置
- 没有过时的语言规则保留
- 最终安装确实匹配仓库技术栈

## Superpowers 核心技能的快速分类

Ultrapowers 核心技能（`ideation`、`task-decomposition`、`plan-execution`、`delegate-and-verify`、`red-green-cycle`、`root-cause-analysis`、`pre-completion-check`、`branch-wrap-up`、`branch-isolation`、`code-review-request`、`review-feedback`、`skill-authoring`、`skill-bootstrapping`、`parallel-dispatch`）对所有项目都是 **DAILY** — 它们是语言无关的工作流技能。

领域特定技能（语言模式、框架模式、工具特定技能）应按项目分类。

## 集成

**相关技能：**
- **ultrapowers:context-budget** — 量化正确技能分类的令牌节省
- **ultrapowers:skill-stocktake** — 分类后审计技能质量
