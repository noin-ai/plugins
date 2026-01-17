---
description: Code review using reviewer agent
argument-hint: [file|staged] [--quick] [--security]
allowed-tools:
  - Task
  - Read
  - Bash
  - TodoWrite
  - AskUserQuestion
---

Thorough code review for quality, security, and best practices.

## Usage

```
/review                    # Review staged changes
/review src/auth.ts        # Review specific file
/review --quick            # Critical issues only
/review --security         # Security-focused review
```

## Execution

```
Skill: review
Args: $ARGUMENTS
```
