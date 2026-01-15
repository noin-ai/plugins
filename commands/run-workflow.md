---
name: run-workflow
description: 执行用户自定义的工作流。读取 YAML 定义文件，按步骤调用 agents 完成任务。
user_invocable: true
arguments:
  - name: name
    description: 工作流名称
    required: true
  - name: scope
    description: 查找范围 (project/user/all)
    type: string
    default: all
---

# /run-workflow - 执行自定义工作流

执行用户创建的工作流定义，按步骤调度 agents 完成任务。

## Usage

```bash
/run-workflow <name>                    # 执行工作流
/run-workflow <name> --url "..."        # 覆盖输入参数
/run-workflow <name> --dry-run          # 预览执行计划，不实际运行
/run-workflow <name> --scope user       # 只查找用户级工作流
```

## 工作流查找顺序

1. `.claude/noin-workflows/<name>.yaml` (项目级)
2. `~/.claude/noin-workflows/<name>.yaml` (用户级)

## 执行流程

```
/run-workflow scrape-prices --url "https://example.com"
    ↓
┌─────────────────────────────────────────────────┐
│ Step 1: 加载工作流定义                           │
│                                                 │
│ 找到: .claude/noin-workflows/scrape-prices.yaml │
│ 版本: 1.0.0                                     │
│ 步骤数: 3                                        │
└─────────────────────────────────────────────────┘
    ↓
┌─────────────────────────────────────────────────┐
│ Step 2: 解析输入参数                             │
│                                                 │
│ inputs:                                         │
│   url: "https://example.com" (从命令行覆盖)      │
│   output_path: "./output/prices.csv" (默认值)   │
└─────────────────────────────────────────────────┘
    ↓
┌─────────────────────────────────────────────────┐
│ Step 3: 执行步骤                                │
│                                                 │
│ [1/3] fetch: 抓取网页                           │
│   → browser-agent.scrape(url)                   │
│   ✓ 完成，提取 15 条数据                        │
│                                                 │
│ [2/3] transform: 转换数据                       │
│   → data-agent.to_csv(data)                     │
│   ✓ 完成，生成 CSV 内容                         │
│                                                 │
│ [3/3] save: 保存文件                            │
│   → file-agent.write(path, content)             │
│   ✓ 完成，保存到 ./output/prices.csv            │
└─────────────────────────────────────────────────┘
    ↓
┌─────────────────────────────────────────────────┐
│ 执行完成                                        │
│                                                 │
│ outputs:                                        │
│   file_path: ./output/prices.csv                │
│   row_count: 15                                 │
│                                                 │
│ 耗时: 12.3s                                     │
└─────────────────────────────────────────────────┘
```

## 执行器逻辑

### 变量解析

```python
# 伪代码
def resolve_variable(expr, context):
    # ${{ inputs.url }} -> context.inputs["url"]
    # ${{ steps.fetch.outputs.data }} -> context.step_outputs["fetch"]["data"]
    # ${{ env.API_KEY }} -> os.environ["API_KEY"]
```

### 步骤执行

```python
# 伪代码
for step in workflow.steps:
    # 检查条件
    if step.condition and not evaluate(step.condition, context):
        skip(step)
        continue

    # 解析输入
    inputs = resolve_inputs(step.inputs, context)

    # 调用 agent
    agent = get_agent(step.agent)
    outputs = agent.execute(step.action, inputs)

    # 保存输出
    context.step_outputs[step.id] = outputs
```

### Agent 调用映射

| Agent | Actions | 说明 |
|-------|---------|------|
| `browser-agent` | scrape, click, fill, screenshot | 浏览器操作 |
| `data-agent` | to_csv, to_json, filter, transform | 数据处理 |
| `file-agent` | read, write, append, delete | 文件操作 |
| `search-agent` | web_search, grep, find | 搜索操作 |
| `codex-coder` | generate, refactor, fix | 代码生成 |
| `gpt52-reviewer` | review, verify, check | 审核验证 |
| `gemini-designer` | design, layout, style | 设计操作 |

## Dry Run 模式

```bash
/run-workflow scrape-prices --dry-run
```

输出：
```
工作流: scrape-prices
版本: 1.0.0

执行计划:
┌────┬───────────────┬─────────────────────────────────────┐
│ #  │ 步骤          │ 操作                                │
├────┼───────────────┼─────────────────────────────────────┤
│ 1  │ fetch         │ browser-agent.scrape                │
│    │               │   url: https://ai.ourines.com       │
├────┼───────────────┼─────────────────────────────────────┤
│ 2  │ transform     │ data-agent.to_csv                   │
│    │               │   input: steps.fetch.outputs.data   │
├────┼───────────────┼─────────────────────────────────────┤
│ 3  │ save          │ file-agent.write                    │
│    │               │   path: ./output/prices.csv         │
└────┴───────────────┴─────────────────────────────────────┘

确认执行？(Y/n)
```

## 错误处理

### 工作流未找到

```
错误: 工作流 "unknown-workflow" 未找到

已检查:
  - .claude/noin-workflows/unknown-workflow.yaml (不存在)
  - ~/.claude/noin-workflows/unknown-workflow.yaml (不存在)

提示: 使用 /list-workflows 查看可用工作流
```

### 步骤执行失败

```
错误: 步骤 "fetch" 执行失败

原因: browser-agent 无法访问 URL
详情: Connection timeout after 30s

策略: stop (配置于 on_error.strategy)

选项:
  1. 重试此步骤
  2. 跳过并继续
  3. 终止执行
```

### 缺少必需输入

```
错误: 缺少必需参数 "url"

工作流 "scrape-prices" 需要以下输入:
  - url (string, 必需): 要抓取的网页 URL

使用方式:
  /run-workflow scrape-prices --url "https://..."
```

## 相关命令

| 命令 | 说明 |
|------|------|
| `/create-workflow` | 交互式创建工作流 |
| `/list-workflows` | 列出可用工作流 |
| `/edit-workflow <name>` | 编辑工作流定义 |
| `/delete-workflow <name>` | 删除工作流 |

## 输出格式

### 成功

```yaml
status: success
workflow: scrape-prices
duration: 12.3s
outputs:
  file_path: ./output/prices.csv
  row_count: 15
steps:
  - id: fetch
    status: success
    duration: 8.1s
  - id: transform
    status: success
    duration: 0.5s
  - id: save
    status: success
    duration: 0.2s
```

### 失败

```yaml
status: failed
workflow: scrape-prices
failed_at: fetch
error:
  type: ConnectionError
  message: Connection timeout
steps:
  - id: fetch
    status: failed
    error: Connection timeout after 30s
```
