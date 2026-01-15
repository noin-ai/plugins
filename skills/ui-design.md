---
name: ui-design
description: Route UI/UX design tasks to Gemini for visual and interaction design
---

# UI Design Skill

This skill triggers the `gemini-designer` agent for UI/UX design tasks.

## When to Trigger

- User asks for UI component design
- New page or view needs layout planning
- Design system or theme creation
- Responsive design requirements
- User interaction flow design
- Visual refresh or redesign requests

## Task Types

### Component Design
- Layout structure
- Responsive behavior
- Styling (Tailwind/CSS)
- States (hover, focus, active, disabled)
- Accessibility considerations

### Page Layout
- Information architecture
- Navigation patterns
- Content hierarchy
- Responsive breakpoints

### Design System
- Color palette
- Typography scale
- Spacing system
- Component variants

## Workflow

```
User: "Design a user profile card"
     ↓
Opus: Route to gemini-designer
     ↓
Gemini: Create design spec with layout, styles, interactions
     ↓
Opus: Route to codex-coder for implementation
     ↓
Codex: Generate component code
     ↓
Opus: Route to gpt52-reviewer for accessibility check
```

## Handoff Format

Gemini outputs design specs that can be directly used by Codex:
- Component structure (JSX/HTML outline)
- Tailwind classes or CSS
- Interaction behaviors
- Accessibility requirements
