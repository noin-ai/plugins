---
name: review
description: Code review using GPT-5.2. Thorough analysis of code quality, security, and best practices.
user_invocable: true
arguments:
  - name: target
    description: File, directory, or "staged" for git staged changes
    required: false
    default: staged
  - name: quick
    description: Quick review focusing on critical issues only
    type: boolean
    default: false
  - name: security
    description: Focus on security vulnerabilities
    type: boolean
    default: false
  - name: accessibility
    description: Focus on UI accessibility
    type: boolean
    default: false
triggers:
  - "review"
  - "check code"
  - "audit"
  - "find bugs"
  - "security check"
---

# Code Review Skill

Trigger thorough code review using `noin-ai:gpt52-reviewer` agent.

## Usage

```bash
/review                    # Review staged changes
/review <file>             # Review specific file
/review <directory>        # Review directory
/review --quick            # Critical issues only
/review --security         # Security-focused
/review --accessibility    # UI accessibility
```

## Review Types

| Flag | Focus |
|------|-------|
| (default) | Full review: correctness, quality, performance |
| `--quick` | Critical issues only |
| `--security` | OWASP Top 10, auth flows, input validation |
| `--accessibility` | WCAG 2.1 AA, ARIA, keyboard nav |

## Workflow

```
/review [target] [flags]
    ↓
Resolve target (file/dir/staged)
    ↓
Task(noin-ai:gpt52-reviewer, review_type, target)
    ↓
Return structured feedback
```

## Output Format

```markdown
## Code Review Summary

**Status**: APPROVE | REQUEST_CHANGES
**Files Reviewed**: X files
**Issues Found**: Y total

### Critical Issues (Must Fix)
1. **[BUG]** `file:line` - Issue description

### Warnings (Should Fix)
1. **[QUALITY]** `file:line` - Concern

### Suggestions (Nice to Have)
1. Consider adding...
```
