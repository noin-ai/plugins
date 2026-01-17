---
description: Simplify code for clarity and maintainability
argument-hint: [file|dir|recent] [--aggressive] [--dry-run]
allowed-tools:
  - Task
  - Read
  - Edit
  - TodoWrite
  - AskUserQuestion
---

Simplify code using the code-simplifier agent.

## Usage

```
/simplify                    # Recently modified code
/simplify src/utils/parser.ts # Specific file
/simplify src/components/     # Directory
/simplify --aggressive        # Allow significant refactoring
/simplify --dry-run           # Preview only, no changes
```

## What It Does

- Remove unnecessary complexity
- Flatten nested conditions
- Extract meaningful names
- Eliminate dead code
- Simplify control flow

## Execution

Invoke the simplify skill:

```
Skill: simplify
Args: $ARGUMENTS
```
