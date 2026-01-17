# Workflow 定义格式

用户自定义工作流的 YAML schema 规范。

## 存储位置

| 范围 | 路径 | 用途 |
|------|------|------|
| 用户级 | `~/.claude/noin-workflows/` | 个人工作流，跨项目使用 |
| 项目级 | `.claude/noin-workflows/` | 项目专属工作流，可版本控制 |

## Schema 定义

```yaml
# workflow 元信息
name: scrape-prices           # 工作流名称（唯一标识）
description: 抓取价格并生成CSV  # 描述
version: 1.0.0
created: 2025-01-16
author: user

# 触发方式
trigger:
  command: /scrape-prices     # 可选：注册为命令
  schedule: "0 9 * * *"       # 可选：定时执行 (cron)
  manual: true                # 手动触发

# 输入参数
inputs:
  - name: url
    type: string
    required: true
    description: 要抓取的网页 URL
    default: https://ai.ourines.com

  - name: output_path
    type: string
    required: false
    default: ./output/prices.csv

# 工作流步骤
steps:
  - id: fetch
    name: 抓取网页
    agent: browser-agent       # 使用的 agent
    action: scrape             # agent 的具体动作
    inputs:
      url: ${{ inputs.url }}
      selectors:
        - name: product_name
          selector: ".product-title"
        - name: price
          selector: ".price"
        - name: description
          selector: ".description"
    outputs:
      - name: raw_data
        type: json

  - id: transform
    name: 数据转换
    agent: data-agent
    action: transform
    inputs:
      data: ${{ steps.fetch.outputs.raw_data }}
      format: csv
      columns:
        - product_name
        - price
        - description
    outputs:
      - name: csv_content
        type: string

  - id: save
    name: 保存文件
    agent: file-agent
    action: write
    inputs:
      path: ${{ inputs.output_path }}
      content: ${{ steps.transform.outputs.csv_content }}
    outputs:
      - name: file_path
        type: string

  - id: verify
    name: 验证结果
    agent: reviewer
    action: verify
    inputs:
      file: ${{ steps.save.outputs.file_path }}
      checks:
        - type: csv_valid
        - type: row_count_min
          value: 1
    condition: ${{ inputs.verify_output == true }}

# 输出定义
outputs:
  file_path: ${{ steps.save.outputs.file_path }}
  row_count: ${{ steps.transform.outputs.row_count }}

# 错误处理
on_error:
  strategy: stop              # stop | continue | retry
  retry_count: 3
  notify: true
```

## 变量引用语法

| 语法 | 说明 |
|------|------|
| `${{ inputs.name }}` | 引用输入参数 |
| `${{ steps.step_id.outputs.name }}` | 引用步骤输出 |
| `${{ env.VAR_NAME }}` | 引用环境变量 |
| `${{ context.project_path }}` | 引用执行上下文 |

## 条件执行

```yaml
steps:
  - id: optional_step
    condition: ${{ inputs.enable_feature == true }}
    # ...
```

## 循环执行

```yaml
steps:
  - id: process_urls
    foreach: ${{ inputs.urls }}
    as: url
    agent: browser-agent
    inputs:
      url: ${{ url }}
```

## 并行执行

```yaml
steps:
  - id: parallel_group
    parallel:
      - id: task_a
        agent: agent-a
      - id: task_b
        agent: agent-b
```

## 内置 Agents

| Agent | 能力 |
|-------|------|
| `browser-agent` | 浏览器操作、网页抓取 |
| `data-agent` | 数据转换、格式化 |
| `file-agent` | 文件读写操作 |
| `coder` | 代码生成 |
| `reviewer` | 内容审核验证 |
| `designer` | UI/内容设计 |
