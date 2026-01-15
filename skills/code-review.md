---
name: code-review
description: Trigger thorough code review using GPT-5.2 for quality assurance
---

# Code Review Skill

This skill triggers the `gpt52-reviewer` agent for thorough code review.

## When to Trigger

### Automatically review when:
- Major feature implementation completed
- Security-sensitive code written
- Complex refactoring done
- Before merging to main branch
- User explicitly requests review

### Review scope:
- Single file changes
- Multi-file feature implementations
- Pull request diffs
- Specific functions or components

## Review Types

### Quick Review
For small changes, focus on:
- Obvious bugs
- Security issues
- Breaking changes

### Full Review
For major changes, cover:
- Correctness and logic
- Security vulnerabilities
- Code quality and maintainability
- Performance implications
- Test coverage

## Usage Pattern

```
User: "Review the changes I just made"
     ↓
Opus: Identify changed files
     ↓
Task → gpt52-reviewer: Perform thorough review
     ↓
Return: Structured feedback with approval status
```

## Integration with Other Agents

- After `codex-coder` generates code → trigger review
- After `codex-max-coder` completes complex task → trigger review
- After `gemini-designer` creates UI code → trigger accessibility review
