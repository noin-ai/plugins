---
name: code-generation
description: Route code generation tasks to the appropriate Codex agent based on complexity
---

# Code Generation Skill

This skill determines when and how to use Codex agents for code generation.

## Routing Logic

### Use `codex-coder` (standard) for:
- Simple feature implementations
- Writing utility functions
- Creating new components from patterns
- Generating tests
- Boilerplate and scaffolding
- Bug fixes with clear solutions

### Use `codex-max-coder` (advanced) for:
- Complex multi-file changes
- Performance-critical implementations
- Security-sensitive code (auth, crypto, validation)
- Major refactoring
- Architectural changes
- Tasks requiring deep context understanding

## Workflow

1. **Analyze complexity**: Estimate if task is simple or complex
2. **Select agent**: Route to appropriate Codex variant
3. **Provide context**: Include relevant files and requirements
4. **Review output**: For critical code, follow up with `gpt52-reviewer`

## Example Triggers

- "Write a function to..." → `codex-coder`
- "Implement the login feature" → `codex-coder` or `codex-max-coder` based on scope
- "Refactor the entire auth system" → `codex-max-coder`
- "Fix this null pointer bug" → `codex-coder`
- "Optimize the database queries" → `codex-max-coder`
