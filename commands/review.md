---
name: review
description: Code review using GPT-5.2. Thorough analysis of code quality, security, and best practices.
arguments:
  - name: target
    description: File, directory, or git diff to review
    required: false
  - name: quick
    description: Quick review focusing on critical issues only
    type: boolean
    default: false
---

# /review - Code Review Command

Trigger thorough code review using GPT-5.2.

## Usage

```
/review                     # Review staged changes
/review <file>              # Review specific file
/review <directory>         # Review all files in directory
/review --quick             # Quick review (critical issues only)
```

## Examples

```
/review src/auth/login.ts
/review src/components/
/review --quick
```

## Behavior

1. Identify target files (staged changes if not specified)
2. Dispatch to `gpt52-reviewer` agent
3. Return structured feedback:
   - Summary
   - Critical issues (must fix)
   - Suggestions (nice to have)
   - Approval status

## Review Checklist

- Correctness and logic
- Security vulnerabilities
- Code quality
- Performance concerns
- Test coverage
