---
description: 列出所有自定义工作流
argument-hint: [--scope project|user|all] [--verbose]
allowed-tools:
  - Glob
  - Read
---

显示已创建的自定义工作流。

## Usage

```
/list-workflows                 # 列出所有
/list-workflows --scope project # 仅项目级
/list-workflows --verbose       # 显示详情
```

## Execution

```
Skill: list-workflows
Args: $ARGUMENTS
```
