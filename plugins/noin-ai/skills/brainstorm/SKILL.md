---
name: brainstorm
description: Multi-agent brainstorming with Critic, Creative, and Pragmatist perspectives
user_invocable: true
arguments:
  - name: topic
    description: The question or idea to brainstorm
    required: true
  - name: rounds
    description: Number of debate rounds (1-3)
    type: number
    default: 2
  - name: output
    description: Output format (summary, plan, todos)
    default: summary
triggers:
  - "brainstorm"
  - "debate"
  - "multi-agent discussion"
  - "get different perspectives"
---

# Brainstorm Skill

You are orchestrating a multi-agent brainstorm. Use these steps as a guide, adapting when user intent requires it.

## Step 1: Clarify If Needed

If the topic is unclear or too broad, use `AskUserQuestion` to clarify. Otherwise proceed.

## Step 2: Track Progress

Use `TodoWrite` to track rounds:

```json
[
  {"content": "Round 1: Gather initial perspectives", "status": "in_progress", "activeForm": "Gathering perspectives"},
  {"content": "Round 2: Cross-debate", "status": "pending", "activeForm": "Cross-debating"},
  {"content": "Synthesize recommendations", "status": "pending", "activeForm": "Synthesizing"}
]
```

Skip Round 2 if `--rounds=1` or user requests a quick take.

## Step 3: Round 1 - Parallel Agent Calls

Launch 3 agents in parallel using a **single message with multiple Task calls**:

| Agent | Role | Focus |
|-------|------|-------|
| `noin-ai:reviewer` | Critic | Risks, security, edge cases |
| `noin-ai:designer` | Creative | UX, elegance, innovation |
| `noin-ai:coder` | Pragmatist | Implementation cost, feasibility |

**Prompt for each agent** (include topic, rounds, output format):
```
You are the [ROLE] in a brainstorm about: [TOPIC]

Constraints: [rounds] rounds, output=[format]

Provide:
1. Key observations (2-3 points)
2. Main concerns or opportunities
3. Initial recommendation

Keep to 3-5 bullet points. You'll see others' views in Round 2.
```

## Step 4: Round 2 - Cross-Debate

Update TodoWrite (mark Round 1 complete, Round 2 in_progress).

Each agent receives Round 1 outputs:
```
You are the [ROLE]. Others said:

**Critic:** [summary]
**Creative:** [summary]
**Pragmatist:** [summary]

Respond to disagreements, acknowledge valid points, refine your recommendation.
```

## Step 5: Synthesize

YOU (Opus) synthesize. Do NOT delegate synthesis.

### --output=summary (default)
```markdown
## Brainstorm: [Topic]

### Consensus
- [Agreed points]

### Key Debates
| Issue | Critic | Creative | Pragmatist |
|-------|--------|----------|------------|

### Recommendations
1. **Do first**: ...
2. **Consider**: ...
3. **Avoid**: ...

### Next Steps
- [ ] Action 1
- [ ] Action 2
```

### --output=plan
Use `EnterPlanMode` to create a structured implementation plan.

### --output=todos
Use `TodoWrite` to create actionable items.

## Flexibility Guidelines

- **Adapt to context**: If user wants only one perspective, use one agent
- **Use native tools**: Prefer `EnterPlanMode` for complex plans, `AskUserQuestion` for decisions
- **Model flexibility**: Agents route to appropriate models by default, but honor user preferences
- **Skip unnecessary rounds**: Early consensus? Skip remaining rounds
- **Concise outputs**: 3-5 bullets per agent, avoid essays
