---
name: list-workflows
description: 列出所有可用的自定义工作流。
user_invocable: true
arguments:
  - name: scope
    description: 查找范围 (project/user/all)
    type: string
    default: all
  - name: verbose
    description: 显示详细信息
    type: boolean
    default: false
triggers:
  - "list workflows"
  - "show workflows"
  - "my workflows"
---

# List Workflows Skill

显示所有已创建的自定义工作流。

## Usage

```bash
/list-workflows                 # 列出所有
/list-workflows --scope project # 仅项目级
/list-workflows --verbose       # 显示详情
```
