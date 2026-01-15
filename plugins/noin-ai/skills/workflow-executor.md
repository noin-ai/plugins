---
name: workflow-executor
description: 执行用户自定义工作流。解析 YAML 定义，按步骤调度 agents，管理变量和状态。
triggers:
  - "run-workflow"
  - "execute workflow"
  - "workflow"
---

# Workflow Executor Skill

核心执行引擎，负责解析和执行用户自定义的工作流定义。

## Trigger Detection

当以下情况时激活此 skill：
- 用户调用 `/run-workflow <name>`
- 用户调用 `/create-workflow` 创建后的测试执行
- Workflow 定时触发（如果支持）

## 核心职责

1. **加载工作流定义**：从指定路径读取 YAML 文件
2. **解析变量引用**：处理 `${{ }}` 语法
3. **调度 Agent 执行**：按顺序或并行调用 agents
4. **管理执行状态**：跟踪每个步骤的输入输出
5. **错误处理**：根据策略处理失败情况

## 执行流程

```
/run-workflow scrape-prices --url "https://example.com"
    ↓
┌─────────────────────────────────────────────────┐
│ Phase 1: 加载和解析                              │
│                                                 │
│ 1. 查找 workflow 文件                           │
│    - .claude/noin-workflows/scrape-prices.yaml  │
│    - ~/.claude/noin-workflows/scrape-prices.yaml│
│                                                 │
│ 2. 解析 YAML 结构                               │
│ 3. 验证必需参数                                  │
│ 4. 合并命令行参数和默认值                        │
└─────────────────────────────────────────────────┘
    ↓
┌─────────────────────────────────────────────────┐
│ Phase 2: 构建执行计划                            │
│                                                 │
│ 1. 解析步骤依赖关系                              │
│ 2. 识别可并行执行的步骤                          │
│ 3. 验证 agent 可用性                            │
│ 4. 生成执行顺序                                  │
└─────────────────────────────────────────────────┘
    ↓
┌─────────────────────────────────────────────────┐
│ Phase 3: 执行步骤                                │
│                                                 │
│ for each step in execution_plan:               │
│   1. 解析输入变量 ${{ ... }}                    │
│   2. 检查条件 (condition)                       │
│   3. 调用 agent.action(inputs)                  │
│   4. 存储输出到 context                         │
│   5. 更新进度                                   │
└─────────────────────────────────────────────────┘
    ↓
┌─────────────────────────────────────────────────┐
│ Phase 4: 完成和报告                              │
│                                                 │
│ 1. 收集所有输出                                  │
│ 2. 生成执行摘要                                  │
│ 3. 返回结果给用户                                │
└─────────────────────────────────────────────────┘
```

## 变量解析系统

### 变量语法

```yaml
${{ inputs.url }}                      # 输入参数
${{ steps.fetch.outputs.data }}        # 步骤输出
${{ env.API_KEY }}                     # 环境变量
${{ context.project_path }}            # 执行上下文
${{ context.timestamp }}               # 当前时间戳
```

### 解析逻辑

```python
# 伪代码
def resolve_variable(expr: str, context: ExecutionContext) -> Any:
    """
    解析变量表达式

    Args:
        expr: 变量表达式，如 "inputs.url"
        context: 执行上下文，包含 inputs, steps, env 等

    Returns:
        解析后的值
    """
    parts = expr.split(".")
    root = parts[0]

    if root == "inputs":
        return context.inputs.get(parts[1])
    elif root == "steps":
        step_id = parts[1]
        if parts[2] == "outputs":
            return context.step_outputs[step_id].get(parts[3])
    elif root == "env":
        return os.environ.get(parts[1])
    elif root == "context":
        return getattr(context, parts[1])

    raise VariableNotFound(expr)
```

### 嵌套变量

```yaml
# 支持嵌套引用
url: ${{ steps.get_config.outputs.base_url }}/${{ inputs.path }}
```

## 执行上下文

```yaml
ExecutionContext:
  workflow:
    name: scrape-prices
    version: 1.0.0
    path: .claude/noin-workflows/scrape-prices.yaml

  inputs:
    url: "https://example.com"
    output_path: "./output/prices.csv"

  step_outputs:
    fetch:
      data: [...]
      status: success
    transform:
      content: "..."
      row_count: 15

  context:
    project_path: /path/to/project
    timestamp: 2025-01-16T10:30:00Z
    user: noin-ai

  env:
    # 从环境变量读取
```

## Agent 调度

### 调用格式

```yaml
# 对于每个步骤
step:
  id: fetch
  agent: browser-agent
  action: scrape
  inputs:
    url: ${{ inputs.url }}

# 转换为 agent 调用
agent_call:
  agent: browser-agent
  input:
    action: scrape
    inputs:
      url: "https://example.com"  # 已解析
```

### Agent 路由表

| Agent | 支持的 Actions |
|-------|---------------|
| `browser-agent` | scrape, click, fill, screenshot, navigate |
| `data-agent` | to_csv, to_json, filter, transform, validate, merge, aggregate |
| `file-agent` | read, write, append, delete, list |
| `search-agent` | web_search, grep, find |
| `codex-coder` | generate, refactor, fix |
| `gpt52-reviewer` | review, verify, check |
| `gemini-designer` | design, layout, style |

## 条件执行

```yaml
step:
  id: optional_step
  condition: ${{ inputs.enable_feature == true }}
  agent: some-agent
  action: some_action

# 解析条件
# 1. 提取表达式: inputs.enable_feature == true
# 2. 解析变量: inputs.enable_feature -> false
# 3. 求值: false == true -> false
# 4. 结果: 跳过此步骤
```

### 支持的条件运算符

| 运算符 | 示例 |
|--------|------|
| `==` | `${{ inputs.mode == "full" }}` |
| `!=` | `${{ steps.check.outputs.valid != false }}` |
| `>`, `<`, `>=`, `<=` | `${{ steps.count.outputs.value > 0 }}` |
| `&&`, `\|\|` | `${{ inputs.a && inputs.b }}` |
| `!` | `${{ !inputs.skip }}` |

## 循环执行

```yaml
step:
  id: process_urls
  foreach: ${{ inputs.urls }}
  as: current_url
  agent: browser-agent
  action: scrape
  inputs:
    url: ${{ current_url }}

# 执行逻辑
# for url in inputs.urls:
#   execute step with current_url = url
#   collect outputs
```

## 并行执行

```yaml
step:
  id: parallel_scrape
  parallel:
    - id: scrape_site1
      agent: browser-agent
      inputs:
        url: https://site1.com
    - id: scrape_site2
      agent: browser-agent
      inputs:
        url: https://site2.com

# 执行逻辑
# 同时启动两个 agent 调用
# 等待全部完成
# 合并输出
```

## 错误处理策略

### 配置

```yaml
on_error:
  strategy: stop | continue | retry
  retry_count: 3
  retry_delay: 1000  # ms
  notify: true
```

### 策略说明

| 策略 | 行为 |
|------|------|
| `stop` | 立即终止，返回错误 |
| `continue` | 记录错误，继续下一步 |
| `retry` | 重试指定次数 |

### 错误恢复

```yaml
# 步骤级错误处理
step:
  id: risky_step
  agent: browser-agent
  on_error:
    fallback:
      agent: data-agent
      action: use_cached
      inputs:
        cache_key: last_scrape_result
```

## 进度报告

执行过程中实时报告进度：

```
[1/4] ⏳ fetch: 抓取网页...
[1/4] ✓ fetch: 完成 (3.2s)
[2/4] ⏳ transform: 转换数据...
[2/4] ✓ transform: 完成 (0.5s)
[3/4] ⏳ validate: 验证数据...
[3/4] ✓ validate: 完成 (0.3s)
[4/4] ⏳ save: 保存文件...
[4/4] ✓ save: 完成 (0.1s)

✓ 工作流执行完成 (4.1s)
```

## 执行结果格式

```yaml
status: success | failed | partial
workflow: scrape-prices
duration: 4.1s

inputs:
  url: "https://example.com"
  output_path: "./output/prices.csv"

outputs:
  file_path: "./output/prices.csv"
  row_count: 15

steps:
  - id: fetch
    status: success
    duration: 3.2s
    outputs:
      data: [...]
  - id: transform
    status: success
    duration: 0.5s
  - id: validate
    status: success
    duration: 0.3s
  - id: save
    status: success
    duration: 0.1s
    outputs:
      path: "./output/prices.csv"

errors: []  # 如果有错误
```

## 与其他 Skills 集成

### 与 code-generation

```yaml
# 工作流中使用代码生成
step:
  id: generate_parser
  agent: codex-coder
  action: generate
  inputs:
    task: "Generate a parser for the scraped data format"
    context:
      sample_data: ${{ steps.scrape.outputs.data }}
```

### 与 code-review

```yaml
# 工作流执行后自动审核
step:
  id: review_output
  agent: gpt52-reviewer
  action: verify
  inputs:
    target: ${{ steps.save.outputs.path }}
    checks:
      - type: csv_valid
      - type: data_quality
```
