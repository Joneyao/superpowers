---
name: requesting-code-review
description: 在完成任务、实现主要功能或合并前使用，以验证工作是否满足需求
---

# 请求代码审查

派遣 superpowers:code-reviewer 子代理来在问题扩散之前发现它们。审查者获得精心构建的评估上下文——而非你的会话历史。这使审查者专注于工作成果而非你的思考过程，同时保留你自己的上下文以继续工作。

**核心原则：** 尽早审查，频繁审查。

## 何时请求审查

**必须审查：**
- 在子代理驱动开发中完成每个任务后
- 完成主要功能后
- 合并到 main 之前

**可选但有价值：**
- 遇到困难时（获取新视角）
- 重构之前（基线检查）
- 修复复杂 bug 之后

## 如何请求

**1. 获取 git SHA：**
```bash
BASE_SHA=$(git rev-parse HEAD~1)  # or origin/main
HEAD_SHA=$(git rev-parse HEAD)
```

**2. 派遣 code-reviewer 子代理：**

使用 Task 工具配合 superpowers:code-reviewer 类型，填写 `code-reviewer.md` 中的模板

**占位符：**
- `{WHAT_WAS_IMPLEMENTED}` - 你刚刚构建的内容
- `{PLAN_OR_REQUIREMENTS}` - 它应该做什么
- `{BASE_SHA}` - 起始提交
- `{HEAD_SHA}` - 结束提交
- `{DESCRIPTION}` - 简要摘要

**3. 根据反馈采取行动：**
- 立即修复严重问题
- 在继续之前修复重要问题
- 记录次要问题留待后续处理
- 如果审查者有误，可以反驳（附上理由）

## 示例

```
[Just completed Task 2: Add verification function]

You: Let me request code review before proceeding.

BASE_SHA=$(git log --oneline | grep "Task 1" | head -1 | awk '{print $1}')
HEAD_SHA=$(git rev-parse HEAD)

[Dispatch superpowers:code-reviewer subagent]
  WHAT_WAS_IMPLEMENTED: Verification and repair functions for conversation index
  PLAN_OR_REQUIREMENTS: Task 2 from docs/superpowers/plans/deployment-plan.md
  BASE_SHA: a7981ec
  HEAD_SHA: 3df7661
  DESCRIPTION: Added verifyIndex() and repairIndex() with 4 issue types

[Subagent returns]:
  Strengths: Clean architecture, real tests
  Issues:
    Important: Missing progress indicators
    Minor: Magic number (100) for reporting interval
  Assessment: Ready to proceed

You: [Fix progress indicators]
[Continue to Task 3]
```

## 与工作流的集成

**子代理驱动开发：**
- 每个任务完成后审查
- 在问题累积之前发现它们
- 修复后再进入下一个任务

**执行计划：**
- 每批（3个任务）完成后审查
- 获取反馈，应用修复，继续

**临时开发：**
- 合并前审查
- 遇到困难时审查

## 危险信号

**绝不要：**
- 因为"很简单"就跳过审查
- 忽略严重问题
- 在未修复的重要问题存在时继续推进
- 与合理的技术反馈争论

**如果审查者有误：**
- 用技术理由反驳
- 展示证明其有效的代码/测试
- 请求澄清

参见模板：requesting-code-review/code-reviewer.md
