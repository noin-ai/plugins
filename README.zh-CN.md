# noin-ai

Claude Code 多智能体插件，由 [noin.ai](https://noin.ai) 提供 API 支持。

[![GitHub](https://img.shields.io/badge/GitHub-noin--ai%2Fplugins-blue)](https://github.com/noin-ai/plugins)
[![English](https://img.shields.io/badge/English-README-blue)](README.md)

## 什么是 noin.ai？

[noin.ai](https://noin.ai) 是一个 AI 模型 API 平台，提供 GPT-5.2、Codex、Gemini 等模型的统一访问接口。

**核心优势：**
- 无需额外 API Key - 认证由 noin.ai 处理
- 多智能体协作 - 不同任务自动路由到专业模型
- 开箱即用 - 安装后立即可用

---

## 快速开始

### 安装

```bash
# 添加插件源并安装
/plugin marketplace add noin-ai/plugins
/plugin install noin-ai
```

### 立即体验

```bash
# 生成代码
/code 写一个邮箱验证函数

# 代码审查
/review

# 头脑风暴
/brainstorm Redis 还是 PostgreSQL 做 session 存储？
```

---

## 智能体

| 智能体 | 模型 | 用途 |
|--------|------|------|
| `codex-coder` | Codex | 常规代码生成 |
| `codex-max-coder` | Codex (高级) | 复杂/安全敏感代码 |
| `gpt52-reviewer` | GPT-5.2 | 代码审查、安全分析 |
| `gemini-designer` | Gemini | UI/UX 设计 |
| `browser-agent` | - | 网页抓取、截图 |
| `data-agent` | - | 数据转换、CSV/JSON 处理 |

---

## 命令

### 代码开发

| 命令 | 说明 |
|------|------|
| `/code <任务>` | 生成代码 |
| `/code --complex <任务>` | 使用高级 Codex 处理复杂任务 |
| `/review` | 审查暂存的更改 |
| `/review <文件>` | 审查指定文件 |
| `/review --security` | 安全专项审查 |
| `/design <组件>` | 创建 UI 设计规范 |
| `/design --implement` | 设计并生成代码 |
| `/simplify` | 简化最近修改的代码 |
| `/simplify <文件>` | 简化指定文件 |
| `/simplify --aggressive` | 允许更大幅度的重构 |

### 多智能体头脑风暴

| 命令 | 说明 |
|------|------|
| `/brainstorm <主题>` | 3 个智能体辩论：批评者 + 创意者 + 务实者 |
| `/brainstorm --rounds 3` | 扩展到 3 轮讨论 |
| `/brainstorm --output plan` | 输出为实施计划 |

### 工作流

| 命令 | 说明 |
|------|------|
| `/create-workflow` | 交互式创建自定义工作流 |
| `/run-workflow <名称>` | 执行已保存的工作流 |
| `/list-workflows` | 列出可用工作流 |

---

## 架构

```
用户请求
    ↓
Claude Opus（协调者）
    ├── 分析任务复杂度
    ├── 路由到专业智能体
    └── 整合结果
         ↓
    ┌────────────────────────────────────┐
    │ 代码智能体                          │
    │ ├── codex-coder                    │
    │ ├── codex-max-coder                │
    │ ├── gpt52-reviewer                 │
    │ └── gemini-designer                │
    ├────────────────────────────────────┤
    │ 头脑风暴模式                        │
    │ ├── 批评者 (gpt52-reviewer)        │
    │ ├── 创意者 (gemini-designer)       │
    │ └── 务实者 (codex-max-coder)       │
    ├────────────────────────────────────┤
    │ 工作流智能体                        │
    │ ├── browser-agent                  │
    │ └── data-agent                     │
    └──────────────────────────────────���─┘
```

---

## 术语表

| 术语 | 定义 |
|------|------|
| **Agent（智能体）** | 处理特定任务的 AI 模型封装 |
| **Skill（技能）** | 决定请求由哪个智能体处理的路由规则 |
| **Command（命令）** | 用户使用的 `/斜杠` 命令 |
| **Workflow（工作流）** | YAML 定义的智能体动作序列 |

---

## 自定义工作流

创建可复用的自动化工作流，串联多个智能体。

### 示例：网页抓取

```bash
# 创建工作流
/create-workflow 抓取网页并转换为 CSV

# 运行
/run-workflow scrape-prices --url "https://example.com"
```

### 存储位置

| 范围 | 路径 |
|------|------|
| 项目级 | `.claude/noin-workflows/` |
| 用户级 | `~/.claude/noin-workflows/` |

---

## 常见问题

**Q: 需要 API Key 吗？**
A: 不需要。插件使用 noin.ai 托管的 API，无需额外配置。

**Q: 支持哪些模型？**
A: GPT-5.2、Codex、Codex-max、Gemini。详见 [智能体](#智能体) 表格。

**Q: 如何反馈问题？**
A: 在 [github.com/noin-ai/plugins/issues](https://github.com/noin-ai/plugins/issues) 提交 issue。

---

## 开发

```bash
git clone https://github.com/noin-ai/plugins.git
cd plugins
/plugin install . --scope local
```

---

## 许可证

MIT
