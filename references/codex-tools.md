# Codex 工具映射

技能使用 Claude Code 工具名称。当你在技能中遇到这些工具时，请使用你的平台等效工具：

| 技能引用 | Codex 等效工具 |
|---|---|
| `Task` 工具（派发子代理） | `spawn_agent`（参见[子代理派发需要多代理支持](#子代理派发需要多代理支持)） |
| 多个 `Task` 调用（并行） | 多个 `spawn_agent` 调用 |
| Task 返回结果 | `wait_agent` |
| Task 自动完成 | `close_agent` 释放槽位 |
| `TodoWrite`（任务跟踪） | `update_plan` |
| `Skill` 工具（调用技能） | 技能原生加载——只需按照说明操作 |
| `Read`、`Write`、`Edit`（文件） | 使用你的原生文件工具 |
| `Bash`（运行命令） | 使用你的原生 shell 工具 |

## 子代理派发需要多代理支持

添加到你的 Codex 配置（`~/.codex/config.toml`）：

```toml
[features]
multi_agent = true
```

这启用了 `spawn_agent`、`wait_agent` 和 `close_agent`，用于 `dispatching-parallel-agents` 和 `subagent-driven-development` 等技能。

旧版说明：`rust-v0.115.0` 之前的 Codex 构建将生成的代理等待暴露为 `wait`。当前 Codex 对生成的代理使用 `wait_agent`。`wait` 名称现在属于代码模式 `exec/wait`，它通过 `cell_id` 恢复一个已让出的 exec 单元；它不是生成代理的结果工具。

## 环境检测

创建 worktree 或完成分支的技能应在继续之前使用只读 git 命令检测其环境：

```bash
GIT_DIR=$(cd "$(git rev-parse --git-dir)" 2>/dev/null && pwd -P)
GIT_COMMON=$(cd "$(git rev-parse --git-common-dir)" 2>/dev/null && pwd -P)
BRANCH=$(git branch --show-current)
```

- `GIT_DIR != GIT_COMMON` → 已在链接的 worktree 中（跳过创建）
- `BRANCH` 为空 → 分离的 HEAD（无法从沙箱分支/推送/PR）

查看 `using-git-worktrees` 第 0 步和 `finishing-a-development-branch` 第 1 步了解每个信号的使用方式。

## Codex 应用完成

当沙箱阻止分支/推送操作（在外部管理的 worktree 中分离 HEAD）时，代理提交所有工作并告知用户使用应用的原生控件：

- **"创建分支"**——命名分支，然后通过应用 UI 提交/推送/PR
- **"移交给本地"**——将工作转移到用户的本地检出

代理仍然可以运行测试、暂存文件并输出建议的分支名称、提交消息和 PR 描述供用户复制。
