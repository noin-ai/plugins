---
description: Generate code using coder agents
argument-hint: <task> [--complex]
allowed-tools:
  - Task
  - TodoWrite
  - AskUserQuestion
  - EnterPlanMode
---

Generate code using the appropriate agent.

## Usage

```
/code write a date formatting utility
/code add unit tests for UserService
/code --complex refactor the payment system
```

## Agent Selection

- Standard tasks → `coder`
- `--complex` flag or security/refactor tasks → `coder-advanced`

## Execution

```
Skill: code
Args: $ARGUMENTS
```
