---
name: code-generation
description: Route code generation tasks to the appropriate Codex agent based on complexity
triggers:
  - "write code"
  - "implement"
  - "create function"
  - "generate"
  - "build feature"
  - "add functionality"
  - "fix bug"
  - "refactor"
---

# Code Generation Skill

This skill determines when and how to use Codex agents for code generation tasks.

## Trigger Detection

Activate this skill when user requests involve:
- Writing new code or functions
- Implementing features
- Creating components
- Generating tests
- Fixing bugs
- Refactoring code

**Keywords**: write, implement, create, generate, build, add, fix, refactor, code, function, component

## Routing Logic

### Route to `codex-coder` (standard) when:

| Condition | Example |
|-----------|---------|
| Single file change | "Add a validation function" |
| Clear requirements | "Create a date formatter" |
| Utility functions | "Write a debounce helper" |
| Component from spec | "Implement this design" |
| Test generation | "Add tests for UserService" |
| Simple bug fix | "Fix this null check" |

### Route to `codex-max-coder` (advanced) when:

| Condition | Example |
|-----------|---------|
| Multi-file changes | "Refactor auth system" |
| Security-sensitive | "Implement JWT auth" |
| Performance-critical | "Optimize database queries" |
| Architectural changes | "Add caching layer" |
| Complex logic | "Implement payment flow" |
| `--complex` flag | "/code --complex ..." |

## Workflow

```
User Request
    ↓
Opus: Analyze task
    ↓
┌─────────────────────────────────────────┐
│ Complexity Check                        │
│ - Single file? → codex-coder            │
│ - Security sensitive? → codex-max-coder │
│ - Performance critical? → codex-max     │
│ - Multi-file refactor? → codex-max      │
│ - --complex flag? → codex-max           │
└─────────────────────────────────────────┘
    ↓
Selected Agent executes
    ↓
┌─────────────────────────────────────────┐
│ Post-execution                          │
│ - Critical code? → trigger gpt52-review │
│ - Simple utility? → return directly     │
└─────────────────────────────────────────┘
    ↓
Opus: Return final result
```

## Agent Invocation

### Standard Task → codex-coder

```yaml
agent: codex-coder
input:
  task: <user request>
  context:
    files: <relevant files from codebase>
    constraints: <project conventions>
  options:
    auto_review: false
    test_required: <based on project settings>
```

### Complex Task → codex-max-coder

```yaml
agent: codex-max-coder
input:
  task: <user request>
  complexity: high
  context:
    files: <relevant files>
    dependencies: <affected modules>
    security_requirements: <if applicable>
  options:
    require_review: true
    incremental: true
```

## Auto-Review Triggers

Automatically invoke `gpt52-reviewer` after code generation when:
- Security-sensitive code generated
- `codex-max-coder` completed task
- User explicitly requests review
- Critical path code modified

## Examples

### Simple Function
```
User: "Write a function to validate email addresses"
→ Skill: Route to codex-coder (single utility function)
→ codex-coder: Generate validation function
→ Return: Code with optional test
```

### Complex Feature
```
User: "Implement user authentication with OAuth"
→ Skill: Route to codex-max-coder (security-sensitive)
→ codex-max-coder: Implement auth system
→ gpt52-reviewer: Security audit
→ Return: Implementation with review
```

### Explicit Complex Flag
```
User: "/code --complex refactor the payment module"
→ Skill: Route to codex-max-coder (explicit flag)
→ codex-max-coder: Careful refactoring
→ gpt52-reviewer: Full review
→ Return: Refactored code with review
```

## Error Handling

If `codex-coder` determines task is too complex:
1. Return partial result with escalation recommendation
2. Opus re-routes to `codex-max-coder`
3. Continue with advanced agent
