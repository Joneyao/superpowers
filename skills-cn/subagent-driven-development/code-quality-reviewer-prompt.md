# 代码质量审查提示模板

在派遣代码质量审查子代理时使用此模板。

**目的：** 验证实现是否构建良好（整洁、经过测试、可维护）

**仅在规格合规审查通过后才派遣。**

```
Task tool (superpowers:code-reviewer):
  Use template at requesting-code-review/code-reviewer.md

  WHAT_WAS_IMPLEMENTED: [from implementer's report]
  PLAN_OR_REQUIREMENTS: Task N from [plan-file]
  BASE_SHA: [commit before task]
  HEAD_SHA: [current commit]
  DESCRIPTION: [task summary]
```

**除了标准代码质量关注点外，审查者还应检查：**
- 每个文件是否有一个明确的职责和定义良好的接口？
- 单元是否被分解为可以独立理解和测试的部分？
- 实现是否遵循了计划中的文件结构？
- 此次实现是否创建了已经很大的新文件，或显著增大了现有文件？（不要标记已有的文件大小——专注于此次变更贡献的部分。）

**代码审查者返回：** 优点、问题（严重/重要/次要）、评估
