---
name: brainstorm
description: Multi-agent debate where Critic, Creative, and Pragmatist discuss a problem through 2-3 rounds
user_invocable: true
arguments:
  - name: topic
    description: The question or idea to brainstorm
    required: true
  - name: rounds
    description: Number of debate rounds (2-3)
    type: number
    default: 2
  - name: output
    description: Output format (summary, plan, todos)
    default: summary
---

# /brainstorm - Multi-Agent Debate Command

Start a multi-agent brainstorming session where 3 AI agents with different perspectives debate a problem.

## Usage

```bash
/brainstorm <topic>
/brainstorm <topic> --rounds 3
/brainstorm <topic> --output plan
```

## Arguments

| Argument | Type | Required | Default | Description |
|----------|------|----------|---------|-------------|
| `topic` | string | Yes | - | The question or problem to discuss |
| `--rounds` | number | No | 2 | Number of debate rounds (2-3) |
| `--output` | string | No | summary | Output format: summary, plan, todos |

## Agent Roles

| Agent | Role | Perspective |
|-------|------|-------------|
| GPT-5.2 | **Critic** | Quality, security, risks, edge cases |
| Gemini | **Creative** | User experience, elegance, innovation |
| Codex-Max | **Pragmatist** | Implementation, performance, effort |

## Workflow

```
/brainstorm <topic>
    |
    v
+------------------------------------------+
| Round 1 (Parallel)                       |
|                                          |
| Critic: "Risk is..."                     |
| Creative: "UX could..."                  |
| Pragmatist: "Implementation cost..."    |
+------------------------------------------+
    |
    v
+------------------------------------------+
| Round 2 (Parallel)                       |
|                                          |
| Each agent sees others' Round 1 output   |
| Responds, challenges, refines position   |
+------------------------------------------+
    |
    v
+------------------------------------------+
| Synthesis                                |
|                                          |
| - Consensus points                       |
| - Remaining debates                      |
| - Recommendations                        |
+------------------------------------------+
    |
    v
Output: summary | plan.md | TodoWrite
```

## Examples

### Technology Decision

```bash
/brainstorm Should we use Redis or PostgreSQL for session storage?
```

**Round 1:**
- Critic: "Redis persistence risks, PostgreSQL more reliable"
- Creative: "Redis speed improves UX significantly"
- Pragmatist: "Team knows PostgreSQL, lower learning curve"

**Round 2:**
- Critic: "Agree with team factor, but Redis cluster complexity..."
- Creative: "Address reliability with Redis + backup strategy"
- Pragmatist: "Backup adds ops burden, stick with simple"

**Synthesis:**
- Consensus: Team experience matters
- Debate: Performance vs simplicity
- Recommendation: Start PostgreSQL, migrate later if needed

### Feature Design

```bash
/brainstorm How to design a user feedback system? --output plan
```

Outputs `plan.md` with phased implementation.

### Architecture Review

```bash
/brainstorm Should we adopt microservices? --rounds 3
```

3 rounds for deeper debate on complex topic.

## Output Formats

### summary (default)

```markdown
## Brainstorm: <topic>

### Consensus
- All agree on X
- Unanimous on Y

### Debates
| Topic | Critic | Creative | Pragmatist |
|-------|--------|----------|------------|
| A     | For    | Against  | Neutral    |

### Recommendations
1. Do this first
2. Consider carefully
3. Avoid this
```

### plan

Generates structured implementation plan as markdown.

### todos

Creates TodoWrite items for actionable next steps.

## Tips

1. **Be specific**: "How to handle auth?" < "JWT vs session cookies for mobile app auth?"
2. **Use 3 rounds** for architecture decisions
3. **Use --output plan** when you'll implement afterward
4. **Follow up with /code** to implement recommendations
