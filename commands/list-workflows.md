---
name: list-workflows
description: 列出所有可用的自定义工作流。显示项目级和用户级工作流。
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
---

# /list-workflows - 列出可用工作流

显示所有已创建的自定义工作流，支持按范围过滤。

## Usage

```bash
/list-workflows                    # 列出所有工作流
/list-workflows --scope project    # 只显示项目级工作流
/list-workflows --scope user       # 只显示用户级工作流
/list-workflows --verbose          # 显示详细信息
```

## 输出格式

### 简洁模式（默认）

```
可用工作流:

项目级 (.claude/noin-workflows/):
  • scrape-prices      抓取价格生成CSV
  • daily-report       生成每日报告
  • backup-data        备份数据到云端

用户级 (~/.claude/noin-workflows/):
  • my-template        个人工作流模板
  • quick-scrape       快速抓取工具

共 5 个工作流

使用方式: /run-workflow <name>
```

### 详细模式（--verbose）

```
可用工作流:

┌─────────────────────────────────────────────────────────────────┐
│ 项目级 (.claude/noin-workflows/)                                │
├─────────────────────────────────────────────────────────────────┤
│ scrape-prices                                                   │
│   描述: 抓取价格生成CSV                                          │
│   版本: 1.0.0                                                   │
│   创建: 2025-01-16                                              │
│   步骤: 3 (browser-agent → data-agent → file-agent)            │
│   输入: url (必需), output_path                                  │
│   使用: /run-workflow scrape-prices --url "..."                 │
├─────────────────────────────────────────────────────────────────┤
│ daily-report                                                    │
│   描述: 生成每日报告                                             │
│   版本: 1.2.0                                                   │
│   创建: 2025-01-10                                              │
│   步骤: 5                                                       │
│   输入: date, format                                            │
│   使用: /run-workflow daily-report                              │
└─────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────┐
│ 用户级 (~/.claude/noin-workflows/)                              │
├─────────────────────────────────────────────────────────────────┤
│ my-template                                                     │
│   描述: 个人工作流模板                                           │
│   版本: 1.0.0                                                   │
│   创建: 2025-01-15                                              │
│   步骤: 2                                                       │
│   输入: (无)                                                     │
│   使用: /run-workflow my-template                               │
└─────────────────────────────────────────────────────────────────┘

共 3 个工作流
```

## 执行逻辑

```
/list-workflows
    ↓
┌─────────────────────────────────────────────────────────────────┐
│ 1. 扫描工作流目录                                                │
│                                                                 │
│    project_dir = .claude/noin-workflows/                        │
│    user_dir = ~/.claude/noin-workflows/                         │
│                                                                 │
│    根据 --scope 参数决定扫描哪些目录                             │
└─────────────────────────────────────────────────────────────────┘
    ↓
┌─────────────────────────────────────────────────────────────────┐
│ 2. 解析每个 YAML 文件                                           │
│                                                                 │
│    for each .yaml file:                                        │
│      - 读取元信息 (name, description, version)                  │
│      - 统计步骤数                                                │
│      - 提取输入参数                                              │
└─────────────────────────────────────────────────────────────────┘
    ↓
┌─────────────────────────────────────────────────────────────────┐
│ 3. 格式化输出                                                    │
│                                                                 │
│    根据 --verbose 参数选择输出格式                               │
└─────────────────────────────────────────────────────────────────┘
```

## 空结果处理

```
没有找到任何工作流。

创建工作流:
  /create-workflow <描述>

示例:
  /create-workflow 抓取网页价格生成CSV
```

## 错误处理

### 目录不存在

```
⚠️ 工作流目录不存在

项目级: .claude/noin-workflows/ (不存在)
用户级: ~/.claude/noin-workflows/ (不存在)

使用 /create-workflow 创建第一个工作流。
```

### YAML 解析错误

```
⚠️ 部分工作流文件解析失败:

  • broken-workflow.yaml: YAML 语法错误 (line 15)

其他工作流正常加载。
```

## 相关命令

| 命令 | 说明 |
|------|------|
| `/create-workflow` | 交互式创建工作流 |
| `/run-workflow <name>` | 执行工作流 |
| `/edit-workflow <name>` | 编辑工作流定义 |
| `/delete-workflow <name>` | 删除工作流 |

## 输出数据结构

```yaml
workflows:
  project:
    - name: scrape-prices
      description: 抓取价格生成CSV
      version: 1.0.0
      created: 2025-01-16
      path: .claude/noin-workflows/scrape-prices.yaml
      steps_count: 3
      inputs:
        - name: url
          required: true
        - name: output_path
          required: false
          default: ./output/prices.csv
  user:
    - name: my-template
      description: 个人工作流模板
      version: 1.0.0
      path: ~/.claude/noin-workflows/my-template.yaml
      steps_count: 2
      inputs: []

total_count: 2
```
