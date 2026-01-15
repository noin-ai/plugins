---
name: ui-design
description: Route UI/UX design tasks to Gemini for visual and interaction design
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

This skill triggers the `gemini-designer` agent for UI/UX design tasks.

## Trigger Detection

Activate this skill when user requests involve:
- UI component design
- Page layout planning
- Design system creation
- Responsive design requirements
- User interaction flows
- Visual refresh or redesign

**Keywords**: design, UI, UX, layout, component, interface, style, responsive, theme, color, typography

## Routing Logic

### Design-Only Tasks

Route directly to `gemini-designer` when:
- User wants design spec without implementation
- Planning phase for new features
- Design system token creation
- Visual exploration

### Design + Implementation Tasks

Trigger multi-agent workflow when:
- `/design --implement` flag used
- User asks to "build" or "create" UI
- Implementation is explicitly requested

## Workflow

### Design Only

```
User: /design user profile card
    ↓
Opus: Route to gemini-designer
    ↓
gemini-designer: Create design spec
    ↓
Return: Structured design document
```

### Design + Implement

```
User: /design --implement login form
    ↓
Opus: Route to gemini-designer
    ↓
gemini-designer: Create design spec
    ↓
Opus: Pass spec to codex-coder
    ↓
codex-coder: Implement component
    ↓
Opus: Route to gpt52-reviewer
    ↓
gpt52-reviewer: Accessibility review
    ↓
Opus: Return design + code + review
```

## Agent Invocation

### Design Request

```yaml
agent: gemini-designer
input:
  component: <what to design>
  context:
    project_style: <detected from existing code>
    framework: <React, Vue, etc.>
    css_framework: <Tailwind, CSS Modules, etc.>
    existing_components: <related components found>
  options:
    implement: <true if --implement>
    responsive: true
    dark_mode: <detected from project>
```

### Implementation Handoff

When `--implement` is true, after gemini-designer completes:

```yaml
agent: codex-coder
input:
  task: "Implement component from design spec"
  context:
    files: <project component directory>
    design_spec: <output from gemini-designer>
  options:
    auto_review: true  # accessibility review
```

## Context Detection

Before invoking gemini-designer, detect:

| Context | How to Detect |
|---------|---------------|
| Framework | package.json (react, vue, svelte) |
| CSS Framework | tailwind.config, postcss.config |
| Existing Design | Look for design tokens, theme files |
| Component Patterns | Existing component structure |

## Task Types

### Component Design
```
/design button with loading state
→ gemini-designer: States, styling, accessibility
```

### Page Layout
```
/design dashboard page layout
→ gemini-designer: Grid, navigation, responsive
```

### Design System
```
/design color palette for dark mode
→ gemini-designer: Color tokens, contrast ratios
```

### Responsive Design
```
/design mobile navigation menu
→ gemini-designer: Mobile-first, breakpoints
```

## Output Expectations

The skill ensures `gemini-designer` returns:

```yaml
component: <name>
status: complete | needs_clarification

layout:
  structure: <visual structure>
  hierarchy: <element nesting>

responsive:
  mobile: <layout changes>
  tablet: <layout changes>
  desktop: <layout changes>

styling:
  tailwind: <class recommendations>
  tokens: <design tokens used>

states:
  default: <description>
  hover: <description>
  focus: <description>
  # ... other states

accessibility:
  aria_labels: [...]
  keyboard: <navigation>
  color_contrast: <ratio>

implementation_notes: <for codex-coder>
```

## Integration with Other Skills

### With code-generation
```
gemini-designer: Design complete
→ code-generation skill: Activate for implementation
→ codex-coder: Build from spec
```

### With code-review
```
codex-coder: Implementation complete
→ code-review skill: Trigger accessibility review
→ gpt52-reviewer: WCAG compliance check
```

## Examples

### Simple Component
```
User: "Design a notification badge"
→ Skill: Route to gemini-designer
→ gemini-designer: Create badge design spec
→ Return: Design with states and styling
```

### Full Implementation Flow
```
User: "/design --implement modal dialog"
→ gemini-designer: Modal design spec
→ codex-coder: Implement Modal component
→ gpt52-reviewer: Accessibility review
→ Return: Complete modal with code and review
```

### Design System Task
```
User: "Create a spacing scale for the project"
→ Skill: Route to gemini-designer
→ gemini-designer: Generate spacing tokens
→ Return: Tailwind config or CSS variables
```

## Error Handling

If requirements are unclear:
1. `gemini-designer` returns `status: needs_clarification`
2. List specific questions about:
   - Component scope
   - Interaction requirements
   - Existing design constraints
3. Provide partial spec for clear parts
