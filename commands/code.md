---
name: code
description: Generate code using Codex agents. Use /code for standard tasks, /code --complex for advanced.
user_invocable: true
arguments:
  - name: task
    description: What code to generate
    required: true
  - name: complex
    description: Use codex-max for complex tasks
    type: boolean
    default: false
---

# /code - Code Generation Command

Generate code using the appropriate Codex agent based on task complexity.

## Usage

```bash
/code <task description>
/code --complex <task description>
```

## Arguments

| Argument | Type | Required | Description |
|----------|------|----------|-------------|
| `task` | string | Yes | Description of what code to generate |
| `--complex` | flag | No | Force use of codex-max-coder |

## Examples

```bash
# Standard code generation
/code write a utility function to format dates
/code implement user avatar component
/code add unit tests for UserService

# Complex tasks (explicit flag)
/code --complex refactor the entire payment processing system
/code --complex implement OAuth2 authentication flow
/code --complex optimize database query performance
```

## Workflow

```
/code <task>
    ↓
Opus: Parse command and analyze task
    ↓
┌─────────────────────────────────────────────────┐
│ Agent Selection                                 │
│                                                 │
│ Use codex-max-coder if:                         │
│   • --complex flag present                      │
│   • Security-sensitive keywords detected        │
│   • Multi-file refactoring indicated            │
│   • Performance optimization requested          │
│   • Architectural changes mentioned             │
│                                                 │
│ Otherwise: Use codex-coder                      │
└─────────────────────────────────────────────────┘
    ↓
Selected agent executes task
    ↓
┌─────────────────────────────────────────────────┐
│ Post-execution                                  │
│                                                 │
│ If codex-max-coder was used:                    │
│   → Auto-trigger gpt52-reviewer                 │
│                                                 │
│ If security-sensitive code generated:           │
│   → Auto-trigger security review                │
│                                                 │
│ Otherwise:                                      │
│   → Return result directly                      │
└─────────────────────────────────────────────────┘
    ↓
Opus: Format and return final output
```

## Complexity Detection

The command auto-detects complexity based on keywords:

### Routes to codex-coder

- "add", "create", "write" + single function/component
- "fix" + specific bug
- "test" + specific module
- Utility functions
- Simple CRUD operations

### Routes to codex-max-coder

- "refactor" + system/module
- "auth", "security", "crypto", "password"
- "optimize", "performance"
- "migrate", "upgrade"
- Multi-file operations
- Architectural terms

## Agent Communication

### To codex-coder

```yaml
agent: codex-coder
input:
  task: <parsed task description>
  context:
    files: <auto-detected relevant files>
    constraints: <project conventions from CLAUDE.md>
  options:
    auto_review: false
    test_required: <from project settings>
```

### To codex-max-coder

```yaml
agent: codex-max-coder
input:
  task: <parsed task description>
  complexity: high
  context:
    files: <auto-detected relevant files>
    dependencies: <detected affected modules>
    security_requirements: <if security-related>
  options:
    require_review: true
    incremental: true
```

## Output Format

The command returns structured output:

```
## Generated Code

### Files Modified
- `path/to/file.ts` - [created|modified]: Brief description

### Code
\`\`\`typescript
// Generated code here
\`\`\`

### Tests (if generated)
\`\`\`typescript
// Test code here
\`\`\`

### Review (if triggered)
- Status: APPROVE | REQUEST_CHANGES
- Issues: [list if any]

### Notes
- Any additional context or warnings
```

## Error Handling

### Task Too Complex for codex-coder

```
codex-coder: "This task requires multi-file changes"
    ↓
Opus: Auto-escalate to codex-max-coder
    ↓
codex-max-coder: Complete the task
    ↓
Return: Result with escalation note
```

### Unclear Requirements

```
Agent: "Requirements unclear, need clarification"
    ↓
Opus: Ask user for specifics
    ↓
User: Provides clarification
    ↓
Retry with clarified requirements
```

## Integration with Other Commands

### With /design

```bash
# Design first, then implement
/design user card component
# ... review design ...
/code implement the designed user card
```

### With /review

```bash
# Generate code
/code implement auth middleware
# Explicit review
/review src/middleware/auth.ts
```

## Best Practices

1. **Be specific**: Clear task descriptions get better results
2. **Use --complex wisely**: Don't over-use; let auto-detection work
3. **Review critical code**: Security-sensitive code is auto-reviewed
4. **Iterate**: Break large tasks into smaller /code commands
