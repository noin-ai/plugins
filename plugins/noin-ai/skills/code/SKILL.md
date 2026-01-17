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
| Simple task, single file | `noin-ai:coder` |
| `--complex` flag | `noin-ai:coder-advanced` |
| Security-sensitive | `noin-ai:coder-advanced` |
| Multi-file refactor | `noin-ai:coder-advanced` |
| Performance optimization | `noin-ai:coder-advanced` |

## Workflow

```
/code <task>
    ↓
Analyze task complexity
    ↓
┌─────────────────────────────────────────────────┐
│ Route to appropriate agent:                     │
│                                                 │
│ coder: utilities, components, tests       │
│ coder-advanced: refactor, security, perf       │
└─────────────────────────────────────────────────┘
    ↓
Agent executes task
    ↓
Auto-review if security-sensitive
    ↓
Return result
```

## Complexity Detection Keywords

### Routes to coder
- "add", "create", "write" + single function
- "fix" + specific bug
- "test" + specific module
- Utility functions

### Routes to coder-advanced
- "refactor" + system/module
- "auth", "security", "crypto"
- "optimize", "performance"
- "migrate", "upgrade"

## Examples

```bash
# Standard tasks → coder
/code write a date formatting utility
/code add unit tests for UserService

# Complex tasks → coder-advanced
/code --complex refactor the payment system
/code implement OAuth2 authentication
```

## Post-Execution

- `coder-advanced` used → auto-trigger `reviewer`
- Security-sensitive code → auto-trigger security review
