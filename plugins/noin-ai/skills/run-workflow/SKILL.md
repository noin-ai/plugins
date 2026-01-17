---
name: run-workflow
description: 执行用户自定义的工作流。按步骤调用 agents 完成任务。
user_invocable: true
arguments:
  - name: name
    description: 工作流名称
    required: true
  - name: scope
    description: 查找范围 (project/user/all)
    type: string
    default: all
triggers:
  - "run workflow"
  - "execute workflow"
---

# Run Workflow Skill

执行用户创建的工作流定义。

## Usage

```bash
/run-workflow <name>
/run-workflow scrape-prices --url "https://example.com"
```

## 执行流程

1. 查找 workflow YAML 文件
2. 解析变量和参数
3. 按步骤调度 agents
4. 返回执行结果
