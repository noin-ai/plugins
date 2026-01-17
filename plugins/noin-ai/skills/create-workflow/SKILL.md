---
name: create-workflow
description: 交互式创建自定义工作流。通过对话引导用户定义任务步骤。
user_invocable: true
arguments:
  - name: description
    description: 工作流描述（可选，会进入交互模式）
    required: false
  - name: scope
    description: 存储范围 (user/project)
    type: string
    default: project
triggers:
  - "create workflow"
  - "new workflow"
  - "define workflow"
---

# Create Workflow Skill

交互式创建自定义工作流，生成 YAML 定义文件。

## Usage

```bash
/create-workflow                    # 进入交互模式
/create-workflow "抓取并分析数据"    # 带描述
/create-workflow --scope user       # 用户级工作流
```

## 交互流程

1. 询问工作流名称和描述
2. 定义步骤和使用的 agents
3. 配置变量和参数
4. 生成 YAML 文件
5. 可选：立即测试

## 输出

生成 `.claude/noin-workflows/<name>.yaml` 文件。
