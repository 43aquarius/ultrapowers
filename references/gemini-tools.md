# Gemini CLI 工具映射

技能使用 Claude Code 工具名称。当你在技能中遇到这些工具时，请使用你的平台等效工具：

| 技能引用 | Gemini CLI 等效工具 |
|---|---|
| `Read`（文件读取） | `read_file` |
| `Write`（文件创建） | `write_file` |
| `Edit`（文件编辑） | `replace` |
| `Bash`（运行命令） | `run_shell_command` |
| `Grep`（搜索文件内容） | `grep_search` |
| `Glob`（按名称搜索文件） | `glob` |
| `TodoWrite`（任务跟踪） | `write_todos` |
| `Skill` 工具（调用技能） | `activate_skill` |
| `WebSearch` | `google_web_search` |
| `WebFetch` | `web_fetch` |
| `Task` 工具（派发子代理） | `@agent-name`（参见[子代理支持](#子代理支持)） |

## 子代理支持

Gemini CLI 通过 `@` 语法原生支持子代理。使用内置的 `@generalist` 代理派发任何任务——它可以访问所有工具并遵循你提供的提示。

当技能要求派生命名代理类型时，使用 `@generalist` 并附带技能的提示模板中的完整提示：

| 技能指令 | Gemini CLI 等效工具 |
|---|---|
| `Task 工具 (ultrapowers:implementer)` | `@generalist` 附带填充的 `implementer-prompt.md` 模板 |
| `Task 工具 (ultrapowers:spec-reviewer)` | `@generalist` 附带填充的 `spec-reviewer-prompt.md` 模板 |
| `Task 工具 (ultrapowers:code-reviewer)` | `@code-reviewer`（捆绑代理）或 `@generalist` 附带填充的审查提示 |
| `Task 工具 (ultrapowers:code-quality-reviewer)` | `@generalist` 附带填充的 `code-quality-reviewer-prompt.md` 模板 |
| `Task 工具 (general-purpose)` 附带内联提示 | `@generalist` 附带你的内联提示 |

### 提示填充

技能提供带有占位符（如 `{WHAT_WAS_IMPLEMENTED}` 或 `[FULL TEXT of task]`）的提示模板。填充所有占位符并将完整提示作为消息传递给 `@generalist`。提示模板本身包含代理的角色、审查标准和预期输出格式——`@generalist` 将遵循它。

### 并行派发

Gemini CLI 支持并行子代理派发。当技能要求你并行派发多个独立的子代理任务时，在同一提示中一起请求所有那些 `@generalist` 或命名子代理任务。保持依赖任务有序，但不要仅仅为了保留更简单的历史记录而序列化独立的子代理任务。

## 额外的 Gemini CLI 工具

这些工具在 Gemini CLI 中可用但没有 Claude Code 等效工具：

| 工具 | 用途 |
|---|---|
| `list_directory` | 列出文件和子目录 |
| `save_memory` | 跨会话将事实持久化到 GEMINI.md |
| `ask_user` | 请求用户的结构化输入 |
| `tracker_create_task` | 丰富的任务管理（创建、更新、列出、可视化） |
| `enter_plan_mode` / `exit_plan_mode` | 在更改前切换到只读研究模式 |
