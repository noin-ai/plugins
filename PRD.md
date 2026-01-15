# noin-plugins PRD

## 概述

多 Agent 协作系统，利用不同 AI 模型的优势进行任务分发。

## 模型分工

| Model | ID | 特性 | 职责 |
|-------|-----|------|------|
| **Opus** | (主窗口) | 全能，快速决策 | 调度、整合、快速判断 |
| **Codex** | `codex` | gpt5.1-codex，快速 | 标准代码生成 |
| **Codex-max** | `codex-max` | gpt5.1-codex-max，谨慎 | 复杂/安全敏感代码 |
| **GPT-5.2** | `gpt-5.2` | 聪明谨慎但慢 | Code Review |
| **Gemini** | `gemini` | gemini3pro，偏 UI | UI/UX 设计 |

## 架构

```
用户请求
    ↓
Opus (主窗口调度)
    ├─→ /design → gemini-designer → 设计稿
    ├─→ /code → codex-coder / codex-max-coder → 代码
    └─→ /review → gpt52-reviewer → 审查报告
    ↓
Opus 整合输出
```

## 组件

### Agents (`agents/`)

#### 代码开发 Agents

| 文件 | Model | 用途 |
|------|-------|------|
| `codex-coder.md` | codex | 快速代码生成 |
| `codex-max-coder.md` | codex-max | 复杂任务、安全敏感代码 |
| `gpt52-reviewer.md` | gpt-5.2 | 代码质量、安全、性能审查 |
| `gemini-designer.md` | gemini | UI 布局、样式、交互设计 |

#### 工作流 Agents

| 文件 | 用途 |
|------|------|
| `browser-agent.md` | 网页抓取、表单交互、截图、动态页面 |
| `data-agent.md` | 数据转换 (CSV/JSON/YAML)、过滤、聚合、验证 |

### Skills (`skills/`)

| 文件 | 触发条件 | 调用 Agent |
|------|----------|------------|
| `code-generation.md` | 写代码请求 | codex-coder / codex-max-coder |
| `code-review.md` | 审查请求 | gpt52-reviewer |
| `ui-design.md` | UI 设计请求 | gemini-designer |
| `workflow-executor.md` | 执行工作流 | 多 Agent 协作 |

### Commands (`commands/`)

#### 代码开发命令

| 命令 | 功能 |
|------|------|
| `/design` | UI 设计，可选 `--implement` 同时生成代码 |
| `/code` | 代码生成，可选 `--complex` 使用 codex-max |
| `/review` | 代码审查 |

#### 工作流命令

| 命令 | 功能 |
|------|------|
| `/create-workflow` | 交互式创建自定义工作流 |
| `/run-workflow` | 执行已保存的工作流 |
| `/list-workflows` | 列出所有可用工作流 |

## 工作流示例

### 1. 完整组件开发

```
用户: /design --implement 用户登录表单

1. Opus 分发到 gemini-designer
2. Gemini 输出设计稿（布局、样式、交互）
3. Opus 分发到 codex-coder
4. Codex 根据设计稿生成代码
5. Opus 分发到 gpt52-reviewer
6. GPT-5.2 审查代码质量和可访问性
7. Opus 整合输出最终结果
```

### 2. 纯代码生成

```
用户: /code 实现 JWT 认证中间件

1. Opus 判断复杂度 → 选择 codex-max
2. Codex-max 生成安全敏感代码
3. 自动触发 gpt52-reviewer 审查
```

### 3. 代码审查

```
用户: /review src/auth/

1. Opus 分发到 gpt52-reviewer
2. GPT-5.2 分析代码质量、安全、性能
3. 输出审查报告和改进建议
```

## 安装

```bash
# 方式一：添加 marketplace 后安装
/plugin marketplace add noin-ai/plugins
/plugin install noin-plugins@noin-ai/plugins

# 方式二：直接从 GitHub 安装
claude plugin install github:noin-ai/plugins

# 方式三：本地开发安装
git clone https://github.com/noin-ai/plugins.git
claude plugin install ./plugins --scope local
```

## 配置

需要的环境变量（通过内置 `/model` 命令已配置）：
- OpenAI API (codex, codex-max, gpt-5.2)
- Google AI API (gemini)

### 4. 自定义工作流

```
用户: /create-workflow 抓取网页价格生成CSV

交互式创建:
1. 收集需求 (URL、字段、输出格式)
2. 识别所需 agents (browser-agent, data-agent)
3. 生成工作流定义
4. 保存到 .claude/noin-workflows/

用户: /run-workflow scrape-prices --url "https://ai.ourines.com"

执行:
1. 加载工作流定义
2. browser-agent 抓取网页
3. data-agent 转换为 CSV
4. 保存文件并返回结果
```

## 工作流存储

| 范围 | 路径 | 用途 |
|------|------|------|
| 项目级 | `.claude/noin-workflows/` | 项目专属，可版本控制 |
| 用户级 | `~/.claude/noin-workflows/` | 个人工作流，跨项目使用 |

## 路线图

- [x] 基础 agents 和 skills
- [x] commands 实现
- [x] agent 间通信协议（input/output protocols）
- [x] 自定义工作流系统 (create/run/list-workflows)
- [x] browser-agent 和 data-agent
- [ ] 任务队列和并行执行
- [ ] 结果缓存
- [ ] 用户偏好配置
- [ ] 多模型验证工作流
