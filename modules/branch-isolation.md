
# 使用 Git Worktree

## 概述

确保工作发生在隔离的工作区中。优先使用平台的原生 worktree 工具。仅在没有原生工具可用时回退到手动 git worktree。

**核心原则：** 首先检测现有隔离。然后使用原生工具。然后回退到 git。永远不要与 harness 对抗。

**开始时声明：** "我正在使用 using-git-worktrees 技能来设置隔离工作区。"

## 第 0 步：检测现有隔离

**在创建任何东西之前，检查你是否已经在隔离的工作区中。**

```bash
GIT_DIR=$(cd "$(git rev-parse --git-dir)" 2>/dev/null && pwd -P)
GIT_COMMON=$(cd "$(git rev-parse --git-common-dir)" 2>/dev/null && pwd -P)
BRANCH=$(git branch --show-current)
```

**子模块守卫：** `GIT_DIR != GIT_COMMON` 在 git 子模块内也成立。在得出"已经在 worktree 中"的结论之前，验证你不在子模块中：

```bash
# 如果返回路径，你在子模块中，不是 worktree — 视为普通仓库
git rev-parse --show-superproject-working-tree 2>/dev/null
```

**如果 `GIT_DIR != GIT_COMMON`（且不是子模块）：** 你已经在链接的 worktree 中。跳到第 3 步（项目设置）。不要创建另一个 worktree。

用分支状态报告：
- 在分支上："已在 `<path>` 的隔离工作区中，分支 `<name>`。"
- 分离 HEAD："已在 `<path>` 的隔离工作区中（分离 HEAD，外部管理）。完成时需要创建分支。"

**如果 `GIT_DIR == GIT_COMMON`（或在子模块中）：** 你在普通的仓库检出中。

用户是否已经在你的指示中表明了他们的 worktree 偏好？如果没有，在创建 worktree 之前请求同意：

> "你想让我设置一个隔离的 worktree 吗？它可以保护你的当前分支免受更改。"

尊重任何已声明的偏好，不要询问。如果用户拒绝同意，就地工作并跳到第 3 步。

## 第 1 步：创建隔离工作区

**你有两种机制。按这个顺序尝试。**

### 1a. 原生 Worktree 工具（优先）

用户请求了隔离工作区（第 0 步同意）。你是否已经有创建 worktree 的方法？它可能是一个名为 `EnterWorktree`、`WorktreeCreate` 的工具，一个 `/worktree` 命令，或一个 `--worktree` 标志。如果你有，使用它并跳到第 3 步。

原生工具自动处理目录放置、分支创建和清理。当你有原生工具时使用 `git worktree add` 会创建你的 harness 看不到或无法管理的幻像状态。

仅当没有原生 worktree 工具可用时才继续到步骤 1b。

### 1b. Git Worktree 回退

**仅当步骤 1a 不适用时使用** — 你没有原生 worktree 工具。使用 git 手动创建 worktree。

#### 目录选择

按此优先级顺序排列。显式用户偏好始终优先于观察到的文件系统状态。

1. **检查你的指示中声明的 worktree 目录偏好。** 如果用户已经指定了一个，不要询问就使用它。

2. **检查现有的项目本地 worktree 目录：**
   ```bash
   ls -d .worktrees 2>/dev/null     # 首选（隐藏）
   ls -d worktrees 2>/dev/null      # 备选
   ```
   如果找到，使用它。如果两者都存在，`.worktrees` 优先。

3. **检查现有的全局目录：**
   ```bash
   project=$(basename "$(git rev-parse --show-toplevel)")
   ls -d ~/.config/ultrapowers/worktrees/$project 2>/dev/null
   ```
   如果找到，使用它（与遗留全局路径的向后兼容性）。

4. **如果没有其他可用指导**，默认使用项目根目录下的 `.worktrees/`。

#### 安全验证（仅项目本地目录）

**在创建 worktree 之前必须验证目录被忽略：**

```bash
git check-ignore -q .worktrees 2>/dev/null || git check-ignore -q worktrees 2>/dev/null
```

**如果未被忽略：** 添加到 .gitignore，提交更改，然后继续。

**为什么关键：** 防止意外提交 worktree 内容到仓库。

全局目录（`~/.config/ultrapowers/worktrees/`）不需要验证。

#### 创建 Worktree

```bash
project=$(basename "$(git rev-parse --show-toplevel)")

# 根据所选位置确定路径
# 项目本地：path="$LOCATION/$BRANCH_NAME"
# 全局：path="~/.config/ultrapowers/worktrees/$project/$BRANCH_NAME"

git worktree add "$path" -b "$BRANCH_NAME"
cd "$path"
```

**沙箱回退：** 如果 `git worktree add` 因权限错误（沙箱拒绝）而失败，告诉用户沙箱阻止了 worktree 创建，你正在当前目录中工作。然后就地运行设置和基线测试。

## 第 3 步：项目设置

自动检测并运行适当的设置：

```bash
# Node.js
if [ -f package.json ]; then npm install; fi

# Rust
if [ -f Cargo.toml ]; then cargo build; fi

# Python
if [ -f requirements.txt ]; then pip install -r requirements.txt; fi
if [ -f pyproject.toml ]; then poetry install; fi

# Go
if [ -f go.mod ]; then go mod download; fi
```

## 第 4 步：验证干净基线

运行测试以确保工作区以干净状态开始：

```bash
# 使用项目适当的命令
npm test / cargo test / pytest / go test ./...
```

**如果测试失败：** 报告失败，询问是继续还是调查。

**如果测试通过：** 报告就绪。

### 报告

```
Worktree 在 <full-path> 就绪
测试通过（<N> 个测试，0 个失败）
准备实现 <feature-name>
```

## 快速参考

| 情况 | 动作 |
|--------|--------|
| 已在链接的 worktree 中 | 跳过创建（第 0 步） |
| 在子模块中 | 视为普通仓库（第 0 步守卫） |
| 原生 worktree 工具可用 | 使用它（第 1a 步） |
| 没有原生工具 | Git worktree 回退（第 1b 步） |
| `.worktrees/` 存在 | 使用它（验证被忽略） |
| `worktrees/` 存在 | 使用它（验证被忽略） |
| 两者都存在 | 使用 `.worktrees/` |
| 两者都不存在 | 检查指示文件，然后默认 `.worktrees/` |
| 全局路径存在 | 使用它（向后兼容） |
| 目录未被忽略 | 添加到 .gitignore + 提交 |
| 创建时权限错误 | 沙箱回退，就地工作 |
| 基线测试失败 | 报告失败 + 询问 |
| 没有 package.json/Cargo.toml | 跳过依赖安装 |

## 常见错误

### 与 harness 对抗

- **问题：** 当平台已经提供隔离时使用 `git worktree add`
- **修复：** 第 0 步检测现有隔离。第 1a 步优先于原生工具。

### 跳过检测

- **问题：** 在现有 worktree 内创建嵌套 worktree
- **修复：** 在创建任何东西之前始终运行第 0 步

### 跳过忽略验证

- **问题：** Worktree 内容被跟踪，污染 git 状态
- **修复：** 在创建项目本地 worktree 之前始终使用 `git check-ignore`

### 假设目录位置

- **问题：** 创建不一致，违反项目约定
- **修复：** 遵循优先级：现有 > 全局遗留 > 指示文件 > 默认

### 在失败的测试上继续

- **问题：** 无法区分新 bug 和预先存在的问题
- **修复：** 报告失败，获得明确的继续许可

## 注意标志

**绝不：**
- 当第 0 步检测到现有隔离时创建 worktree
- 当你有原生 worktree 工具（例如 `EnterWorktree`）时使用 `git worktree add`。这是 #1 错误 — 如果你有它，使用它。
- 通过直接跳到第 1b 步的 git 命令跳过第 1a 步
- 在没有验证被忽略的情况下创建 worktree（项目本地）
- 跳过基线测试验证
- 在失败的测试上不询问就继续

**始终：**
- 首先运行第 0 步检测
- 优先于 git 回退的原生工具
- 遵循目录优先级：现有 > 全局遗留 > 指示文件 > 默认
- 验证项目本地的目录被忽略
- 自动检测并运行项目设置
- 验证干净的测试基线
