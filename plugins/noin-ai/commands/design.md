---
description: UI/UX design using designer agent
argument-hint: <component> [--implement] [--dark]
allowed-tools:
  - Task
  - TodoWrite
  - AskUserQuestion
  - EnterPlanMode
---

Design UI components and layouts.

## Usage

```
/design user profile card
/design navigation header --dark
/design login form --implement
```

## Flags

- `--implement`: Also generate implementation code
- `--dark`: Include dark mode variant

## Execution

```
Skill: design
Args: $ARGUMENTS
```
