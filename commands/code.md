---
name: code
description: Generate code using Codex agents. Use /code for standard tasks, /code --complex for advanced.
arguments:
  - name: task
    description: What code to generate
    required: true
  - name: complex
    description: Use codex-max for complex tasks
    type: boolean
    default: false
---

# /code - Code Generation Command

Generate code using the appropriate Codex agent.

## Usage

```
/code <task description>
/code --complex <task description>
```

## Examples

```
/code write a utility function to format dates
/code implement user authentication middleware
/code --complex refactor the entire payment processing system
```

## Behavior

1. Parse the task description
2. If `--complex` flag or task appears complex → use `codex-max-coder`
3. Otherwise → use `codex-coder`
4. Generate code following project conventions
5. Optionally trigger `/review` for critical code
