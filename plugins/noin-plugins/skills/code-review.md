---
name: code-review
description: Trigger thorough code review using GPT-5.2 for quality assurance
triggers:
  - "review"
  - "check code"
  - "audit"
  - "look at"
  - "find bugs"
  - "security check"
---

# Code Review Skill

This skill triggers the `gpt52-reviewer` agent for thorough code review.

## Trigger Detection

Activate this skill when user requests involve:
- Code review requests
- Security audits
- Bug finding
- Quality checks
- Pre-merge validation

**Keywords**: review, check, audit, analyze, find bugs, security, quality, look at, examine

## Automatic Triggers

Review is automatically triggered after:
- `codex-coder` completes critical code
- `codex-max-coder` completes any task
- `gemini-designer` + `codex-coder` complete UI code
- Before merge requests

## Review Types

| Type | When to Use | Focus Areas |
|------|-------------|-------------|
| `quick` | Small changes, utilities | Bugs, security, breaking changes |
| `full` | Major features, refactoring | All checklist items |
| `security` | Auth, crypto, validation code | OWASP Top 10, auth flows |
| `accessibility` | UI components | WCAG, keyboard, screen reader |

## Workflow

```
Review Request
    ↓
Opus: Determine review type
    ↓
┌─────────────────────────────────────────┐
│ Review Type Selection                   │
│ - Quick review? → review_type: quick    │
│ - Full review? → review_type: full      │
│ - Security code? → review_type: security│
│ - UI code? → review_type: accessibility │
└─────────────────────────────────────────┘
    ↓
Identify target files
    ↓
gpt52-reviewer: Execute review
    ↓
Return structured feedback
```

## Agent Invocation

### User-Requested Review

```yaml
agent: gpt52-reviewer
input:
  review_type: <quick | full | security | accessibility>
  target:
    type: <files | diff | staged>
    paths: <identified file paths>
  context:
    source_agent: user
    task_description: <what to review>
    security_critical: <detected or specified>
```

### Post-Agent Auto-Review

```yaml
agent: gpt52-reviewer
input:
  review_type: <based on source agent>
  target:
    type: files
    paths: <files modified by previous agent>
  context:
    source_agent: <codex-coder | codex-max-coder | gemini-designer>
    task_description: <original task>
    security_critical: <true if codex-max-coder>
```

## Target Resolution

When no explicit target specified:
1. Check for staged git changes → review staged
2. Check for recent modifications → review modified
3. Check command context → review mentioned paths
4. Ask user for clarification

```
/review                    → Review staged changes
/review src/auth/          → Review directory
/review --quick            → Quick review of staged
/review src/auth/login.ts  → Review specific file
```

## Integration Patterns

### After codex-coder

```
codex-coder: Completed utility function
    ↓
Skill: Determine if review needed
    ├── Critical path? → trigger full review
    ├── Simple utility? → skip or quick review
    └── User requested? → full review
    ↓
gpt52-reviewer: Execute appropriate review
```

### After codex-max-coder

```
codex-max-coder: Completed complex task
    ↓
Skill: Always trigger review (codex-max implies critical)
    ↓
gpt52-reviewer: Full or security review
    ↓
If issues found → route back to codex-max-coder
```

### After gemini-designer + codex-coder

```
gemini-designer + codex-coder: UI component complete
    ↓
Skill: Trigger accessibility review
    ↓
gpt52-reviewer: Accessibility focus
    ↓
Return: Suggestions for ARIA, keyboard, contrast
```

## Response Format

The skill ensures `gpt52-reviewer` returns:

```yaml
status: APPROVE | REQUEST_CHANGES | NEEDS_DISCUSSION
summary: <brief assessment>

critical_issues: [...]  # Must fix
warnings: [...]         # Should fix
suggestions: [...]      # Nice to have
positive_notes: [...]   # Good practices

metrics:
  files_reviewed: <count>
  issues_found: <count>
```

## Examples

### Explicit Review Request
```
User: "Review the auth module"
→ Skill: Full review of src/auth/
→ gpt52-reviewer: Comprehensive analysis
→ Return: Structured feedback
```

### Post-Generation Review
```
codex-max-coder: Completed payment integration
→ Skill: Auto-trigger security review
→ gpt52-reviewer: Security audit
→ Return: Security-focused feedback
```

### Quick Pre-Commit Review
```
User: "/review --quick"
→ Skill: Quick review of staged changes
→ gpt52-reviewer: Critical issues only
→ Return: Fast feedback for commit decision
```
