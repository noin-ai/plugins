---
name: design
description: UI/UX design using Gemini. Creates layouts, components, and design specs.
user_invocable: true
arguments:
  - name: component
    description: What to design
    required: true
  - name: implement
    description: Also generate implementation code
    type: boolean
    default: false
  - name: responsive
    description: Include responsive design
    type: boolean
    default: true
  - name: dark
    description: Include dark mode variant
    type: boolean
    default: false
triggers:
  - "design"
  - "UI"
  - "UX"
  - "layout"
  - "component"
  - "interface"
  - "style"
  - "responsive"
---

# UI Design Skill

Design UI components and layouts using `noin-ai:gemini-designer` agent.

## Usage

```bash
/design <component>              # Design only
/design <component> --implement  # Design + code
/design <component> --dark       # Include dark mode
```

## Workflow

```
/design <component>
    ↓
Task(noin-ai:gemini-designer, component, options)
    ↓
Return design spec
    ↓
If --implement: Task(noin-ai:codex-coder, implement design)
```

## Output

- Visual specification
- Component structure
- Accessibility considerations
- Responsive breakpoints
- Optional: Implementation code

## Examples

```bash
/design user profile card
/design navigation header --dark
/design login form --implement
```
