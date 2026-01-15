---
name: create-workflow
description: 交互式创建自定义工作流。通过对话引导用户定义任务步骤，生成可复用的 workflow 文件。
user_invocable: true
arguments:
  - name: description
    description: 工作流描述（可选，会进入交互模式）
    required: false
  - name: scope
    description: 存储范围 (user/project)
    type: string
    default: project
---

# /create-workflow - 交互式工作流创建

通过对话引导用户创建自定义工作流，生成 YAML 定义文件。

## Usage

```bash
/create-workflow                           # 进入交互模式
/create-workflow 抓取网页价格生成CSV        # 带描述快速创建
/create-workflow --scope user 每日报告     # 创建用户级工作流
```

## 交互流程

```
/create-workflow 抓取网页价格生成CSV
    ↓
┌─────────────────────────────────────────────────┐
│ Step 1: 理解需求                                │
│                                                 │
│ 我理解你想要：                                   │
│ - 抓取网页                                       │
│ - 提取价格信息                                   │
│ - 生成 CSV 文件                                  │
│                                                 │
│ 请确认或补充细节：                               │
│ 1. 目标 URL 是什么？                            │
│ 2. 需要提取哪些字段？                            │
│ 3. 输出文件路径？                                │
└─────────────────────────────────────────────────┘
    ↓
用户回答
    ↓
┌─────────────────────────────────────────────────┐
│ Step 2: 识别所需能力                            │
│                                                 │
│ 这个工作流需要：                                 │
│ ✓ browser-agent: 网页抓取                       │
│ ✓ data-agent: 数据转换                          │
│ ✓ file-agent: 文件写入                          │
│                                                 │
│ 是否需要验证输出？(Y/n)                          │
└─────────────────────────────────────────────────┘
    ↓
┌─────────────────────────────────────────────────┐
│ Step 3: 生成工作流定义                          │
│                                                 │
│ 生成以下工作流：                                 │
│                                                 │
│ name: scrape-prices                             │
│ steps:                                          │
│   1. fetch: 抓取 https://ai.ourines.com        │
│   2. transform: 转换为 CSV                      │
│   3. save: 保存到 ./prices.csv                  │
│                                                 │
│ 确认创建？(Y/n)                                  │
└─────────────────────────────────────────────────┘
    ↓
保存到 .claude/noin-workflows/scrape-prices.yaml
    ↓
┌─────────────────────────────────────────────────┐
│ 创建成功！                                       │
│                                                 │
│ 使用方式：                                       │
│   /run-workflow scrape-prices                   │
│   /run-workflow scrape-prices --url "..."       │
└─────────────────────────────────────────────────┘
```

## 交互式问答模板

### 基础信息收集

```yaml
questions:
  - id: name
    prompt: "给这个工作流起个名字："
    type: text
    validation: "^[a-z0-9-]+$"
    hint: "小写字母、数字和连字符"

  - id: description
    prompt: "简单描述这个工作流的作用："
    type: text

  - id: trigger_type
    prompt: "如何触发这个工作流？"
    type: choice
    options:
      - label: "手动运行 (/run-workflow name)"
        value: manual
      - label: "注册为命令 (/custom-command)"
        value: command
      - label: "定时执行"
        value: schedule
```

### 能力识别

根据用户描述自动识别所需能力：

| 关键词 | 识别能力 | 推荐 Agent |
|--------|---------|------------|
| 抓取、爬取、网页 | 浏览器操作 | browser-agent |
| CSV、Excel、JSON | 数据转换 | data-agent |
| 保存、写入、导出 | 文件操作 | file-agent |
| 搜索、查找 | 网络搜索 | search-agent |
| 写文章、内容 | 内容生成 | writer-agent |
| 检查、验证、审核 | 内容审核 | gpt52-reviewer |
| 设计、样式 | UI设计 | gemini-designer |
| 代码、实现 | 代码生成 | codex-coder |

### 步骤构建

```
检测到需要以下步骤：

1. [browser-agent] 抓取网页
   - URL: https://ai.ourines.com
   - 提取: 产品名称、价格、描述

2. [data-agent] 转换数据
   - 输入: 步骤1的结果
   - 输出: CSV 格式

3. [file-agent] 保存文件
   - 路径: ./output/prices.csv

需要调整步骤顺序或添加新步骤吗？
```

## 存储位置

| Scope | 路径 | 说明 |
|-------|------|------|
| `project` | `.claude/noin-workflows/` | 项目专属，可 git 追踪 |
| `user` | `~/.claude/noin-workflows/` | 用户个人，跨项目使用 |

## 生成的文件示例

```yaml
# .claude/noin-workflows/scrape-prices.yaml
name: scrape-prices
description: 抓取网页价格并生成CSV文件
version: 1.0.0
created: 2025-01-16
author: user

trigger:
  manual: true

inputs:
  - name: url
    type: string
    required: true
    default: https://ai.ourines.com

  - name: output_path
    type: string
    default: ./output/prices.csv

steps:
  - id: fetch
    name: 抓取网页
    agent: browser-agent
    action: scrape
    inputs:
      url: ${{ inputs.url }}
      selectors:
        - name: product_name
          selector: ".product-title"
        - name: price
          selector: ".price"

  - id: transform
    name: 转换为CSV
    agent: data-agent
    action: to_csv
    inputs:
      data: ${{ steps.fetch.outputs.data }}

  - id: save
    name: 保存文件
    agent: file-agent
    action: write
    inputs:
      path: ${{ inputs.output_path }}
      content: ${{ steps.transform.outputs.content }}

outputs:
  file_path: ${{ steps.save.outputs.path }}
```

## 高级功能

### 从现有工作流复制

```bash
/create-workflow --from scrape-prices --name scrape-prices-v2
```

### 导入外部定义

```bash
/create-workflow --import ./my-workflow.yaml
```

### 验证工作流

```bash
/create-workflow --validate scrape-prices
```

## 错误处理

### 名称冲突

```
工作流 "scrape-prices" 已存在。

选择：
1. 覆盖现有工作流
2. 使用新名称 (scrape-prices-2)
3. 取消创建
```

### 能力缺失

```
此工作流需要 browser-agent，但当前未配置。

建议：
1. 安装 Playwright MCP server
2. 或使用 WebFetch 替代（功能受限）
```
