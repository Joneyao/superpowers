# Codex 工具映射

技能使用 Claude Code 工具名称。当你在技能中遇到这些名称时，请使用你平台的对应工具：

| 技能引用 | Codex 对应工具 |
|----------|---------------|
| `Task` tool（派遣子代理） | `spawn_agent`（参见[命名代理派遣](#命名代理派遣)） |
| 多个 `Task` 调用（并行） | 多个 `spawn_agent` 调用 |
| Task 返回结果 | `wait` |
| Task 自动完成 | `close_agent` 释放槽位 |
| `TodoWrite`（任务跟踪） | `update_plan` |
| `Skill` tool（调用技能） | 技能原生加载——直接遵循指令即可 |
| `Read`、`Write`、`Edit`（文件） | 使用你的原生文件工具 |
| `Bash`（运行命令） | 使用你的原生 shell 工具 |

## 子代理派遣需要多代理支持

在你的 Codex 配置（`~/.codex/config.toml`）中添加：

```toml
[features]
multi_agent = true
```

这将启用 `spawn_agent`、`wait` 和 `close_agent`，用于 `dispatching-parallel-agents` 和 `subagent-driven-development` 等技能。

## 命名代理派遣

Claude Code 技能引用命名代理类型，如 `superpowers:code-reviewer`。
Codex 没有命名代理注册表——`spawn_agent` 从内置角色（`default`、`explorer`、`worker`）创建通用代理。

当技能要求派遣命名代理类型时：

1. 找到代理的提示文件（例如 `agents/code-reviewer.md` 或技能的本地提示模板如 `code-quality-reviewer-prompt.md`）
2. 读取提示内容
3. 填充所有模板占位符（`{BASE_SHA}`、`{WHAT_WAS_IMPLEMENTED}` 等）
4. 使用填充后的内容作为 `message` 生成一个 `worker` 代理

| 技能指令 | Codex 对应操作 |
|----------|---------------|
| `Task tool (superpowers:code-reviewer)` | `spawn_agent(agent_type="worker", message=...)` 使用 `code-reviewer.md` 内容 |
| `Task tool (general-purpose)` 带内联提示 | `spawn_agent(message=...)` 使用相同的提示 |

### 消息构建

`message` 参数是用户级输入，不是系统提示。按以下方式构建以最大化指令遵循度：

```
Your task is to perform the following. Follow the instructions below exactly.

<agent-instructions>
[filled prompt content from the agent's .md file]
</agent-instructions>

Execute this now. Output ONLY the structured response following the format
specified in the instructions above.
```

- 使用任务委派框架（"Your task is..."）而非角色框架（"You are..."）
- 用 XML 标签包裹指令——模型将标签块视为权威内容
- 以明确的执行指令结尾，防止模型对指令进行总结

### 何时可以移除此变通方案

此方法是为了弥补 Codex 的插件系统尚不支持 `plugin.json` 中的 `agents` 字段。当 `RawPluginManifest` 增加 `agents` 字段后，插件可以创建指向 `agents/` 的符号链接（与现有的 `skills/` 符号链接类似），技能就可以直接派遣命名代理类型。

## 环境检测

创建 worktree 或完成分支的技能应在继续之前使用只读 git 命令检测其环境：

```bash
GIT_DIR=$(cd "$(git rev-parse --git-dir)" 2>/dev/null && pwd -P)
GIT_COMMON=$(cd "$(git rev-parse --git-common-dir)" 2>/dev/null && pwd -P)
BRANCH=$(git branch --show-current)
```

- `GIT_DIR != GIT_COMMON` → 已在链接的 worktree 中（跳过创建）
- `BRANCH` 为空 → detached HEAD（无法从沙箱进行分支/推送/PR 操作）

参见 `using-git-worktrees` 步骤 0 和 `finishing-a-development-branch` 步骤 1 了解每个技能如何使用这些信号。

## Codex App 完成工作

当沙箱阻止分支/推送操作时（在外部管理的 worktree 中处于 detached HEAD 状态），代理提交所有工作并通知用户使用 App 的原生控件：

- **"Create branch"** ——命名分支，然后通过 App UI 进行提交/推送/PR
- **"Hand off to local"** ——将工作转移到用户的本地检出

代理仍然可以运行测试、暂存文件，并输出建议的分支名称、提交消息和 PR 描述供用户复制。
