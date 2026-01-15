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

| 文件 | Model | 用途 |
|------|-------|------|
| `codex-coder.md` | codex | 快速代码生成 |
| `codex-max-coder.md` | codex-max | 复杂任务、安全敏感代码 |
| `gpt52-reviewer.md` | gpt-5.2 | 代码质量、安全、性能审查 |
| `gemini-designer.md` | gemini | UI 布局、样式、交互设计 |

### Skills (`skills/`)

| 文件 | 触发条件 | 调用 Agent |
|------|----------|------------|
| `code-generation.md` | 写代码请求 | codex-coder / codex-max-coder |
| `code-review.md` | 审查请求 | gpt52-reviewer |
| `ui-design.md` | UI 设计请求 | gemini-designer |

### Commands (`commands/`)

| 命令 | 功能 |
|------|------|
| `/design` | UI 设计，可选 `--implement` 同时生成代码 |
| `/code` | 代码生成，可选 `--complex` 使用 codex-max |
| `/review` | 代码审查 |

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
# GitHub 仓库安装
claude plugins install github:liubiao/noin-plugins
```

## 配置

需要的环境变量（通过内置 `/model` 命令已配置）：
- OpenAI API (codex, codex-max, gpt-5.2)
- Google AI API (gemini)

## 路线图

- [ ] 基础 agents 和 skills
- [ ] commands 实现
- [ ] agent 间通信协议
- [ ] 任务队列和并行执行
- [ ] 结果缓存
- [ ] 用户偏好配置
