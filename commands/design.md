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
    description: Include responsive design (default true)
    type: boolean
    default: true
  - name: dark
    description: Include dark mode variant
    type: boolean
    default: false
---

# /design - UI Design Command

Design UI components and layouts using Gemini, with optional implementation.

## Usage

```bash
/design <component description>
/design --implement <component description>
/design --dark <component description>
/design --implement --dark <component description>
```

## Arguments

| Argument | Type | Required | Default | Description |
|----------|------|----------|---------|-------------|
| `component` | string | Yes | - | What to design |
| `--implement` | flag | No | false | Also generate code |
| `--responsive` | flag | No | true | Include responsive design |
| `--dark` | flag | No | false | Include dark mode variant |

## Examples

```bash
# Design only (get spec)
/design user profile card with avatar and stats
/design responsive navigation header with mobile menu
/design data table with sorting and pagination

# Design + implement
/design --implement modal dialog for confirmation
/design --implement login form with validation
/design --implement --dark settings page layout

# With dark mode
/design --dark notification badge component
```

## Workflow

### Design Only

```
/design <component>
    ↓
Opus: Parse command and detect project context
    ↓
┌─────────────────────────────────────────────────┐
│ Context Detection                               │
│                                                 │
│ • Framework: React/Vue/Svelte (from package)    │
│ • CSS: Tailwind/CSS Modules (from config)       │
│ • Existing patterns: from component directory   │
│ • Design tokens: from theme files               │
└─────────────────────────────────────────────────┘
    ↓
gemini-designer: Create design spec
    ↓
Opus: Format and return design document
```

### Design + Implement

```
/design --implement <component>
    ↓
Opus: Parse command and detect context
    ↓
gemini-designer: Create design spec
    ↓
Opus: Pass spec to codex-coder
    ↓
codex-coder: Implement component from spec
    ↓
Opus: Route to gpt52-reviewer
    ↓
gpt52-reviewer: Accessibility review
    ↓
Opus: Return design + code + review
```

## Agent Communication

### To gemini-designer

```yaml
agent: gemini-designer
input:
  component: <parsed component description>
  context:
    project_style: <detected design patterns>
    framework: <React | Vue | Svelte | etc>
    css_framework: <Tailwind | CSS Modules | etc>
    existing_components: <related components found>
  options:
    implement: <true if --implement>
    responsive: <from --responsive, default true>
    dark_mode: <from --dark>
```

### To codex-coder (if --implement)

```yaml
agent: codex-coder
input:
  task: "Implement component from design spec"
  context:
    files: <component directory>
    design_spec: <output from gemini-designer>
    constraints:
      - Follow existing component patterns
      - Use project's CSS framework
      - Include accessibility attributes
  options:
    auto_review: true
```

### To gpt52-reviewer (if --implement)

```yaml
agent: gpt52-reviewer
input:
  review_type: accessibility
  target:
    type: files
    paths: <generated component files>
  context:
    source_agent: gemini-designer
    task_description: "UI component implementation"
```

## Output Format

### Design Only

```
## Component Design: [Name]

### Layout Structure

\`\`\`
┌─────────────────────────────────────┐
│ Header                              │
├─────────────────────────────────────┤
│  ┌─────┐                            │
│  │Avatar│  Name                     │
│  └─────┘  Description               │
├─────────────────────────────────────┤
│ Stats: X | Y | Z                    │
└─────────────────────────────────────┘
\`\`\`

### Responsive Behavior

| Breakpoint | Changes |
|------------|---------|
| Mobile (<640px) | Stack layout, full width |
| Tablet (640-1024px) | Side-by-side, constrained |
| Desktop (>1024px) | Full layout with sidebar |

### Styling

\`\`\`css
/* Tailwind Classes */
container: "flex flex-col gap-4 p-6 bg-white rounded-lg shadow-md"
header: "text-xl font-semibold text-gray-900"
avatar: "w-16 h-16 rounded-full object-cover"
...
\`\`\`

### States

| State | Appearance |
|-------|------------|
| Default | Normal styling |
| Hover | Subtle shadow increase |
| Focus | Ring outline |
| Loading | Skeleton placeholder |

### Accessibility

- **ARIA**: role="article", aria-labelledby="user-name"
- **Keyboard**: Tab to interactive elements
- **Screen Reader**: Descriptive alt text for avatar
- **Contrast**: 4.5:1 minimum ratio

### Implementation Notes

- Use Next.js Image for avatar optimization
- Consider lazy loading for below-fold cards
- Add skeleton loading state for async data
```

### Design + Implement

```
## Component: [Name]

### Design Spec
[... design output above ...]

---

### Implementation

#### Files Created
- `src/components/UserCard.tsx`
- `src/components/UserCard.test.tsx` (if tests generated)

#### Code

\`\`\`tsx
// src/components/UserCard.tsx
import { FC } from 'react';

interface UserCardProps {
  name: string;
  avatar: string;
  description: string;
  stats: { label: string; value: number }[];
}

export const UserCard: FC<UserCardProps> = ({ name, avatar, description, stats }) => {
  return (
    <article
      className="flex flex-col gap-4 p-6 bg-white rounded-lg shadow-md"
      aria-labelledby="user-name"
    >
      {/* ... implementation ... */}
    </article>
  );
};
\`\`\`

---

### Accessibility Review

**Status**: APPROVE

**Notes**:
- ARIA labels properly implemented
- Keyboard navigation working
- Color contrast meets WCAG AA

**Suggestions**:
- Consider adding focus-visible styles
```

## Design Patterns

### Component Design

```bash
/design button with loading state and icon support
```
- States: default, hover, focus, active, disabled, loading
- Variants: primary, secondary, outline, ghost
- Sizes: sm, md, lg

### Page Layout

```bash
/design dashboard layout with sidebar and header
```
- Responsive grid system
- Navigation patterns
- Content areas

### Design System

```bash
/design color palette with semantic tokens
```
- Color scales (50-900)
- Semantic colors (success, warning, error)
- Dark mode mappings

### Form Design

```bash
/design --implement registration form with validation
```
- Input states (default, focus, error, success)
- Validation messages
- Submit handling

## Error Handling

### Unclear Requirements

```
Need more details about the component:

1. What is the primary purpose?
2. What data will it display?
3. What interactions are needed?
4. Any existing design patterns to follow?

Please provide more context, or I can create a generic version.
```

### Missing Project Context

```
Could not detect project context.

Please specify:
- Framework: React, Vue, Svelte?
- CSS Framework: Tailwind, CSS Modules, styled-components?

Or provide path to existing components for pattern matching.
```

## Best Practices

1. **Be descriptive**: Include data types and interactions in the description
2. **Start design-only**: Review spec before implementing
3. **Use --implement wisely**: Only when you're confident in the design
4. **Consider dark mode**: Use `--dark` for user-facing components
5. **Check accessibility**: Always review the accessibility section
6. **Iterate**: Refine design through multiple `/design` commands before implementing
