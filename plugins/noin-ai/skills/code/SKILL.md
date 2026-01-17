---
name: code
description: Generate code using Codex agents. Use /code for standard tasks, /code --complex for advanced.
user_invocable: true
arguments:
  - name: task
    description: What code to generate
    required: true
  - name: complex
    description: Use codex-max for complex tasks
    type: boolean
    default: false
triggers:
  - "write code"
  - "implement"
  - "create function"
  - "generate"
  - "build feature"
  - "add functionality"
  - "fix bug"
  - "refactor"
---

# Code Generation Skill

Generate code using the appropriate Codex agent based on task complexity.

## Usage

```bash
/code <task description>
/code --complex <task description>
```

## Agent Selection

| Condition | Agent |
|-----------|-------|
| Simple task, single file | `noin-ai:codex-coder` |
| `--complex` flag | `noin-ai:codex-max-coder` |
| Security-sensitive | `noin-ai:codex-max-coder` |
| Multi-file refactor | `noin-ai:codex-max-coder` |
| Performance optimization | `noin-ai:codex-max-coder` |

## Workflow

```
/code <task>
    ↓
Analyze task complexity
    ↓
┌─────────────────────────────────────────────────┐
│ Route to appropriate agent:                     │
│                                                 │
│ codex-coder: utilities, components, tests       │
│ codex-max-coder: refactor, security, perf       │
└─────────────────────────────────────────────────┘
    ↓
Agent executes task
    ↓
Auto-review if security-sensitive
    ↓
Return result
```

## Complexity Detection Keywords

### Routes to codex-coder
- "add", "create", "write" + single function
- "fix" + specific bug
- "test" + specific module
- Utility functions

### Routes to codex-max-coder
- "refactor" + system/module
- "auth", "security", "crypto"
- "optimize", "performance"
- "migrate", "upgrade"

## Examples

```bash
# Standard tasks → codex-coder
/code write a date formatting utility
/code add unit tests for UserService

# Complex tasks → codex-max-coder
/code --complex refactor the payment system
/code implement OAuth2 authentication
```

## Post-Execution

- `codex-max-coder` used → auto-trigger `gpt52-reviewer`
- Security-sensitive code → auto-trigger security review
