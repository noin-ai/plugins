---
name: simplify
description: Simplify code for clarity and maintainability. Preserves functionality while improving readability.
user_invocable: true
arguments:
  - name: target
    description: File, directory, or "recent" for recently modified
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
triggers:
  - "simplify"
  - "clean up"
  - "refactor for clarity"
  - "make readable"
  - "reduce complexity"
---

# Code Simplification Skill

Simplify code using `noin-ai:code-simplifier` agent.

## Usage

```bash
/simplify                    # Recently modified code
/simplify <file>             # Specific file
/simplify --aggressive       # Significant refactoring
/simplify --dry-run          # Preview only
```

## What It Does

- Remove unnecessary complexity
- Flatten nested conditions
- Extract meaningful names
- Eliminate dead code
- Simplify control flow

## Workflow

```
/simplify [target]
    ↓
Resolve target (file/dir/recent)
    ↓
Task(noin-ai:code-simplifier, target, options)
    ↓
Return simplified code or diff
```

## Examples

```bash
/simplify src/utils/parser.ts
/simplify src/components/ --aggressive
/simplify --dry-run
```
