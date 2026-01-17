---
description: 执行自定义工作流
argument-hint: <name> [--args]
allowed-tools:
  - Task
  - Read
  - Glob
  - TodoWrite
---

执行已创建的工作流定义。

## Usage

```
/run-workflow scrape-prices
/run-workflow scrape-prices --url "https://example.com"
```

## Execution

```
Skill: run-workflow
Args: $ARGUMENTS
```
