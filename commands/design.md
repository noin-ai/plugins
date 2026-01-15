---
name: design
description: UI/UX design using Gemini. Creates layouts, components, and design specs.
arguments:
  - name: component
    description: What to design
    required: true
  - name: implement
    description: Also generate implementation code
    type: boolean
    default: false
---

# /design - UI Design Command

Design UI components and layouts using Gemini.

## Usage

```
/design <component description>
/design --implement <component description>
```

## Examples

```
/design a user profile card with avatar, name, and stats
/design responsive navigation header with mobile menu
/design --implement modal dialog for confirmation
```

## Behavior

1. Dispatch to `gemini-designer` agent
2. Generate design spec:
   - Layout structure
   - Responsive behavior
   - Styling (Tailwind/CSS)
   - Interactions and states
   - Accessibility notes

3. If `--implement` flag:
   - Pass design spec to `codex-coder`
   - Generate component code
   - Optionally trigger `/review` for accessibility

## Output Format

```
## Component: [Name]

### Layout
[Structure description or ASCII diagram]

### Styles
[Tailwind classes or CSS]

### States
[Hover, focus, active, disabled]

### Accessibility
[ARIA, keyboard, screen reader notes]
```
