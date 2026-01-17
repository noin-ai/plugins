---
name: brainstorm
description: Multi-agent brainstorming with Critic, Creative, and Pragmatist perspectives
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
triggers:
  - "brainstorm"
  - "debate"
  - "multi-agent discussion"
  - "get different perspectives"
---

# Brainstorm Skill

You are now orchestrating a multi-agent brainstorm. Follow these steps exactly.

## Step 1: Confirm Topic

If the topic is unclear or too broad, use `AskUserQuestion` to clarify:

```
AskUserQuestion:
  question: "What specific aspect of [topic] should we focus on?"
  options: [specific angles based on topic]
```

If the topic is clear, proceed directly.

## Step 2: Set Up Tracking

Use `TodoWrite` to track the brainstorm rounds:

```
TodoWrite:
  - "Round 1: Gather initial perspectives" (in_progress)
  - "Round 2: Cross-debate and refinement" (pending)
  - "Synthesize final recommendations" (pending)
```

## Step 3: Round 1 - Parallel Agent Calls

Launch 3 agents in parallel using a **single message with multiple Task calls**:

| Agent | Role | Prompt Focus |
|-------|------|--------------|
| `noin-ai:gpt52-reviewer` | Critic | Risks, security, edge cases, what could go wrong |
| `noin-ai:gemini-designer` | Creative | User experience, elegance, innovative approaches |
| `noin-ai:codex-max-coder` | Pragmatist | Implementation cost, feasibility, team capacity |

**Prompt template for each agent:**
```
You are the [ROLE] in a multi-agent brainstorm about: [TOPIC]

Provide your perspective:
1. Key observations from your viewpoint
2. Main concerns or opportunities you see
3. Your initial recommendation

Be concise (3-5 bullet points). You'll see others' views in Round 2.
```

## Step 4: Round 2 - Cross-Debate

After Round 1 completes, mark it done in TodoWrite, then launch Round 2.

Each agent receives all Round 1 outputs and responds:

**Prompt template:**
```
You are the [ROLE]. Here's what others said in Round 1:

**Critic said:** [summary]
**Creative said:** [summary]
**Pragmatist said:** [summary]

Your task:
1. Respond to points you disagree with
2. Acknowledge valid points from others
3. Refine your recommendation

Be direct. Constructive disagreement leads to better outcomes.
```

## Step 5: Synthesize

After Round 2, YOU (Opus) synthesize the results. Do NOT delegate synthesis.

Output format based on `--output` flag:

### summary (default)
```markdown
## 🎯 Brainstorm: [Topic]

### Consensus
- [Points all 3 agents agreed on]

### Key Debates
| Issue | Critic | Creative | Pragmatist |
|-------|--------|----------|------------|
| ...   | ...    | ...      | ...        |

### Recommendations
1. **Do first**: [highest priority action]
2. **Consider**: [important but not urgent]
3. **Avoid**: [identified risks]

### Next Steps
- [ ] [Actionable item 1]
- [ ] [Actionable item 2]
```

### plan
Use `EnterPlanMode` and create a structured implementation plan based on the brainstorm conclusions.

### todos
Use `TodoWrite` to create actionable items from the recommendations.

## Important Rules

1. **Always use parallel Task calls** - Send all 3 agent calls in a single message
2. **Track progress with TodoWrite** - Update status after each round
3. **Ask before assuming** - Use AskUserQuestion if topic needs clarification
4. **You synthesize** - Don't delegate the final summary to an agent
5. **Stay concise** - Each agent response should be 3-5 bullet points, not essays
