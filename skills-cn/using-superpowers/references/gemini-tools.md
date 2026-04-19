# Gemini CLI 工具映射

技能使用 Claude Code 工具名称。当你在技能中遇到这些名称时，请使用你平台的对应工具：

| 技能引用 | Gemini CLI 对应工具 |
|----------|-------------------|
| `Read`（文件读取） | `read_file` |
| `Write`（文件创建） | `write_file` |
| `Edit`（文件编辑） | `replace` |
| `Bash`（运行命令） | `run_shell_command` |
| `Grep`（搜索文件内容） | `grep_search` |
| `Glob`（按名称搜索文件） | `glob` |
| `TodoWrite`（任务跟踪） | `write_todos` |
| `Skill` tool（调用技能） | `activate_skill` |
| `WebSearch` | `google_web_search` |
| `WebFetch` | `web_fetch` |
| `Task` tool（派遣子代理） | 无对应工具——Gemini CLI 不支持子代理 |

## 不支持子代理

Gemini CLI 没有与 Claude Code 的 `Task` 工具对应的功能。依赖子代理派遣的技能（`subagent-driven-development`、`dispatching-parallel-agents`）将回退到通过 `executing-plans` 进行单会话执行。

## 额外的 Gemini CLI 工具

这些工具在 Gemini CLI 中可用，但在 Claude Code 中没有对应工具：

| 工具 | 用途 |
|------|------|
| `list_directory` | 列出文件和子目录 |
| `save_memory` | 将信息持久化到 GEMINI.md 以跨会话使用 |
| `ask_user` | 向用户请求结构化输入 |
| `tracker_create_task` | 丰富的任务管理（创建、更新、列表、可视化） |
| `enter_plan_mode` / `exit_plan_mode` | 在进行更改之前切换到只读研究模式 |
