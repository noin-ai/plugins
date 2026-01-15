---
name: review
description: Code review using GPT-5.2. Thorough analysis of code quality, security, and best practices.
user_invocable: true
arguments:
  - name: target
    description: File, directory, or git diff to review
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
---

# /review - Code Review Command

Trigger thorough code review using GPT-5.2 for quality assurance.

## Usage

```bash
/review                           # Review staged changes
/review <file>                    # Review specific file
/review <directory>               # Review all files in directory
/review --quick                   # Quick review (critical issues only)
/review --security                # Security-focused review
/review --accessibility           # Accessibility-focused review
```

## Arguments

| Argument | Type | Required | Default | Description |
|----------|------|----------|---------|-------------|
| `target` | string | No | staged | File, directory, or "staged" |
| `--quick` | flag | No | false | Only check critical issues |
| `--security` | flag | No | false | Focus on security vulnerabilities |
| `--accessibility` | flag | No | false | Focus on UI accessibility |

## Examples

```bash
# Review staged changes before commit
/review

# Review specific file
/review src/auth/login.ts

# Review entire directory
/review src/components/

# Quick pre-commit check
/review --quick

# Security audit of auth module
/review src/auth/ --security

# Accessibility check for UI components
/review src/components/Modal.tsx --accessibility
```

## Workflow

```
/review [target] [flags]
    ↓
Opus: Parse command and resolve target
    ↓
┌─────────────────────────────────────────────────┐
│ Target Resolution                               │
│                                                 │
│ No target specified:                            │
│   → Use git staged changes                      │
│                                                 │
│ File path:                                      │
│   → Review single file                          │
│                                                 │
│ Directory path:                                 │
│   → Review all code files in directory          │
└─────────────────────────────────────────────────┘
    ↓
┌─────────────────────────────────────────────────┐
│ Review Type Selection                           │
│                                                 │
│ --quick:        review_type = quick             │
│ --security:     review_type = security          │
│ --accessibility: review_type = accessibility    │
│ default:        review_type = full              │
└─────────────────────────────────────────────────┘
    ↓
gpt52-reviewer: Execute review
    ↓
Opus: Format and return structured feedback
```

## Review Types

### Quick Review (`--quick`)

Focus on critical issues only:
- Obvious bugs and logic errors
- Security vulnerabilities
- Breaking changes
- Type errors

Best for: Pre-commit checks, small changes

### Full Review (default)

Comprehensive analysis:
- Correctness and logic
- Security vulnerabilities
- Code quality and maintainability
- Performance implications
- Test coverage assessment
- Best practice adherence

Best for: Feature implementations, refactoring

### Security Review (`--security`)

Deep security audit:
- OWASP Top 10 vulnerabilities
- Authentication/authorization flows
- Input validation
- Data handling and exposure
- Dependency vulnerabilities
- Secrets management

Best for: Auth code, API endpoints, data handling

### Accessibility Review (`--accessibility`)

UI accessibility check:
- WCAG 2.1 AA compliance
- ARIA labels and roles
- Keyboard navigation
- Screen reader compatibility
- Color contrast ratios
- Focus management

Best for: UI components, forms, interactive elements

## Agent Communication

```yaml
agent: gpt52-reviewer
input:
  review_type: <quick | full | security | accessibility>
  target:
    type: <files | diff | staged>
    paths: <resolved file paths>
    diff: <git diff if applicable>
  context:
    source_agent: user
    task_description: "Code review requested via /review command"
    security_critical: <true if --security or detected>
```

## Output Format

```
## Code Review Summary

**Status**: APPROVE | REQUEST_CHANGES | NEEDS_DISCUSSION
**Files Reviewed**: X files
**Issues Found**: Y total

---

### Critical Issues (Must Fix)

1. **[BUG]** `src/auth/login.ts:42`
   - **Issue**: Potential null pointer dereference
   - **Fix**: Add null check before accessing user.email

2. **[SECURITY]** `src/api/users.ts:78`
   - **Issue**: SQL injection vulnerability
   - **Fix**: Use parameterized query instead of string concatenation

---

### Warnings (Should Fix)

1. **[QUALITY]** `src/utils/format.ts:15`
   - **Concern**: Function has high cyclomatic complexity
   - **Suggestion**: Extract into smaller helper functions

---

### Suggestions (Nice to Have)

1. `src/components/Button.tsx`
   - Consider adding loading state animation

---

### Positive Notes

- Clean separation of concerns in the auth module
- Good test coverage for utility functions

---

### Metrics

| Metric | Count |
|--------|-------|
| Files Reviewed | X |
| Critical Issues | Y |
| Warnings | Z |
| Suggestions | N |
```

## Integration Patterns

### Auto-Review After /code

```bash
/code --complex implement payment processing
# → codex-max-coder generates code
# → gpt52-reviewer auto-triggered
# → Combined output returned
```

### Review Before Commit

```bash
# Make changes...
git add .
/review --quick
# If APPROVE → proceed with commit
# If REQUEST_CHANGES → fix issues first
```

### Security Audit Workflow

```bash
# Review all auth-related code
/review src/auth/ --security
/review src/middleware/ --security
/review src/api/ --security
```

## Error Handling

### No Staged Changes

```
No staged changes found.

Options:
1. Stage changes: git add <files>
2. Specify files: /review src/path/to/file.ts
3. Review directory: /review src/
```

### File Not Found

```
File not found: src/missing.ts

Did you mean:
- src/missing-file.ts
- src/components/missing.tsx
```

### Large Review Scope

```
Warning: Reviewing 50+ files may take a while.

Options:
1. Continue with full review
2. Use /review --quick for faster results
3. Narrow scope to specific directory
```

## Best Practices

1. **Review before commit**: Use `/review --quick` before each commit
2. **Security for auth**: Always use `--security` for authentication code
3. **Accessibility for UI**: Use `--accessibility` for user-facing components
4. **Iterate on issues**: Fix critical issues first, then re-review
5. **Learn from suggestions**: Positive notes highlight good patterns to follow
