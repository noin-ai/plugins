---
name: simplify
description: Simplify code for clarity and maintainability using Opus. Preserves functionality while improving readability.
user_invocable: true
arguments:
  - name: target
    description: File, directory, or function to simplify
    required: false
    default: recent
  - name: aggressive
    description: Allow more significant refactoring
    type: boolean
    default: false
  - name: dry-run
    description: Show proposed changes without applying
    type: boolean
    default: false
---

# /simplify - Code Simplification Command

Simplify code for clarity, consistency, and maintainability using the code-simplifier agent.

## Usage

```bash
/simplify                          # Simplify recently modified code
/simplify <file>                   # Simplify specific file
/simplify <directory>              # Simplify all files in directory
/simplify --aggressive             # Allow significant refactoring
/simplify --dry-run                # Preview changes without applying
```

## Arguments

| Argument | Type | Required | Default | Description |
|----------|------|----------|---------|-------------|
| `target` | string | No | recent | File, directory, or "recent" |
| `--aggressive` | flag | No | false | Allow significant refactoring |
| `--dry-run` | flag | No | false | Preview changes without applying |

## Examples

```bash
# Simplify recently modified code
/simplify

# Simplify specific file
/simplify src/utils/parser.ts

# Simplify entire directory
/simplify src/helpers/

# Preview changes without applying
/simplify src/auth/login.ts --dry-run

# Allow more aggressive refactoring
/simplify src/components/Form.tsx --aggressive

# Aggressive simplification with preview
/simplify src/legacy/ --aggressive --dry-run
```

## Workflow

```
/simplify [target] [flags]
    ↓
Opus: Parse command and resolve target
    ↓
┌─────────────────────────────────────────────────┐
│ Target Resolution                               │
│                                                 │
│ No target specified:                            │
│   → Identify recently modified files            │
│                                                 │
│ File path:                                      │
│   → Simplify single file                        │
│                                                 │
│ Directory path:                                 │
│   → Simplify all code files in directory        │
└─────────────────────────────────────────────────┘
    ↓
code-simplifier: Analyze and simplify code
    ↓
┌─────────────────────────────────────────────────┐
│ --dry-run:                                      │
│   → Return proposed changes, don't apply        │
│                                                 │
│ Default:                                        │
│   → Apply changes and return summary            │
└─────────────────────────────────────────────────┘
    ↓
gpt52-reviewer: Quick review of changes (optional)
    ↓
Opus: Format and return results
```

## Simplification Types

### Default (Conservative)

Safe simplifications:
- Remove dead code and unused variables
- Flatten unnecessary nesting
- Improve variable names
- Remove redundant comments
- Simplify conditionals

Best for: Regular cleanup, code review follow-up

### Aggressive (`--aggressive`)

More significant refactoring:
- Extract helper functions
- Restructure code organization
- Replace patterns with modern alternatives
- Consolidate related code
- Remove abstraction layers

Best for: Legacy code cleanup, major refactoring

## Agent Communication

```yaml
agent: code-simplifier
input:
  target:
    type: <files | recent_changes>
    paths: <resolved file paths>
    scope: <file | function | module>
  context:
    source_agent: user
    task_description: "Code simplification requested via /simplify command"
  options:
    aggressive: <boolean>
    preserve_comments: true
```

## Output Format

### Normal Mode

```
## Simplification Complete

**Status**: simplified
**Files Processed**: X files
**Lines Reduced**: Y lines

---

### Changes Made

#### `src/utils/parser.ts`
- **Type**: Simplified
- **Before**: 120 lines → **After**: 85 lines
- Flattened nested conditionals in `parseConfig()`
- Extracted repeated validation to `validateInput()`
- Removed 3 unused imports

#### `src/helpers/format.ts`
- **Type**: Cleaned
- **Before**: 45 lines → **After**: 40 lines
- Replaced nested ternary with switch statement
- Improved variable names for clarity

---

### Preserved Functionality

- All existing tests pass
- Public API unchanged
- No behavioral changes

---

### Metrics

| Metric | Value |
|--------|-------|
| Files Processed | X |
| Lines Reduced | Y |
| Complexity Reduction | Medium |
```

### Dry-Run Mode (`--dry-run`)

```
## Simplification Preview (Dry Run)

**Files to Process**: X files
**Estimated Lines Reduced**: Y lines

---

### Proposed Changes

#### `src/utils/parser.ts`

**Change 1**: Flatten nested conditionals (lines 42-58)

Before:
```typescript
if (config) {
  if (config.enabled) {
    if (config.value) {
      return process(config.value);
    }
  }
}
return null;
```

After:
```typescript
if (!config?.enabled || !config.value) {
  return null;
}
return process(config.value);
```

**Change 2**: Extract repeated validation (lines 72-85, 98-111)
- Create `validateInput()` function
- Replace 2 occurrences

---

### Apply Changes?

Run without `--dry-run` to apply these changes:
```bash
/simplify src/utils/parser.ts
```
```

## Integration Patterns

### Post-Implementation Cleanup

```bash
/code implement user registration
# → codex-coder generates code
/simplify src/auth/register.ts
# → code-simplifier cleans up
/review --quick
# → Final verification
```

### Legacy Code Modernization

```bash
# Preview changes first
/simplify src/legacy/ --aggressive --dry-run
# Review proposed changes
# Apply if satisfied
/simplify src/legacy/ --aggressive
```

### Focused Simplification

```bash
# After complex feature implementation
/simplify src/features/checkout/ --aggressive
# Security review after changes
/review src/features/checkout/ --security
```

## Error Handling

### No Recent Changes

```
No recently modified files found.

Options:
1. Specify file: /simplify src/path/to/file.ts
2. Specify directory: /simplify src/
```

### File Not Found

```
File not found: src/missing.ts

Did you mean:
- src/utils/missing.ts
- src/helpers/missing-util.ts
```

### No Simplifications Found

```
No simplifications found for: src/clean-code.ts

This file is already well-structured:
- Clear naming conventions
- Minimal nesting
- Appropriate abstractions
```

## Best Practices

1. **Preview first**: Use `--dry-run` for unfamiliar code
2. **Run tests after**: Verify functionality after simplification
3. **Commit before simplify**: Create checkpoint before aggressive changes
4. **Review significant changes**: Use `/review` after aggressive simplification
5. **Iterate incrementally**: Simplify small sections at a time for large codebases
