---
description: 交互式创建自定义工作流
argument-hint: [description] [--scope user|project]
allowed-tools:
  - Write
  - AskUserQuestion
---

通过对话引导创建自定义工作流 YAML 文件。

## Usage

```
/create-workflow                    # 进入交互模式
/create-workflow "抓取并分析数据"    # 带描述
/create-workflow --scope user       # 用户级工作流
```

## Execution

```
Skill: create-workflow
Args: $ARGUMENTS
```
