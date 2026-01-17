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
triggers:
  - "brainstorm"
  - "debate"
  - "multi-agent discussion"
  - "get different perspectives"
  - "pros and cons"
---

# Brainstorm Skill

Multi-agent debate system inspired by A2A protocol principles. Multiple AI agents with different perspectives discuss a problem through structured rounds, then synthesize their conclusions.

## When to Use

- User needs diverse perspectives on a design decision
- Comparing multiple approaches/technologies
- Exploring pros and cons of an idea
- Complex problem requiring different viewpoints
- User explicitly requests brainstorming or debate

## Agent Roles

| Agent | Role | Perspective |
|-------|------|-------------|
| `gpt52-reviewer` | **Critic** | Quality, security, maintainability, edge cases |
| `gemini-designer` | **Creative** | User experience, elegance, innovation |
| `codex-max-coder` | **Pragmatist** | Implementation feasibility, performance, effort |

## Workflow

```
/brainstorm <问题或想法>
    ↓
┌─────────────────────────────────────────────────────────────────┐
│ Opus: Coordinator                                               │
│                                                                 │
│ 1. Parse the question/topic                                     │
│ 2. Create shared context (contextId)                            │
│ 3. Initiate Round 1                                             │
└─────────────────────────────────────────────────────────────────┘
    ↓
┌─────────────────────────────────────────────────────────────────┐
│ Round 1: Initial Perspectives (Parallel)                        │
│                                                                 │
│ ┌─────────────┐  ┌─────────────┐  ┌─────────────┐              │
│ │ Critic      │  │ Creative    │  │ Pragmatist  │              │
│ │ (GPT-5.2)   │  │ (Gemini)    │  │ (Codex-Max) │              │
│ │             │  │             │  │             │              │
│ │ "考虑风险..." │  │ "用户体验..." │  │ "实现成本..." │              │
│ └─────────────┘  └─────────────┘  └─────────────┘              │
└─────────────────────────────────────────────────────────────────┘
    ↓
┌─────────────────────────────────────────────────────────────────┐
│ Opus: Collect Round 1, identify disagreements                   │
│       Prepare Round 2 prompts with cross-references             │
└─────────────────────────────────────────────────────────────────┘
    ↓
┌─────────────────────────────────────────────────────────────────┐
│ Round 2: Debate & Response (Parallel)                           │
│                                                                 │
│ ┌─────────────┐  ┌─────────────┐  ┌─────────────┐              │
│ │ Critic      │  │ Creative    │  │ Pragmatist  │              │
│ │             │  │             │  │             │              │
│ │ 看到其他人   │  │ 看到其他人   │  │ 看到其他人   │              │
│ │ 的观点并回应 │  │ 的观点并回应 │  │ 的观点并回应 │              │
│ └─────────────┘  └─────────────┘  └─────────────┘              │
└─────────────────────────────────────────────────────────────────┘
    ↓
┌─────────────────────────────────────────────────────────────────┐
│ Round 3: Synthesis (Optional, if still disagreeing)             │
│                                                                 │
│ Each agent: "Given all perspectives, my final recommendation"   │
└─────────────────────────────────────────────────────────────────┘
    ↓
┌─────────────────────────────────────────────────────────────────┐
│ Opus: Final Synthesis                                           │
│                                                                 │
│ - Summarize areas of agreement                                  │
│ - Highlight unresolved debates                                  │
│ - Provide actionable recommendations                            │
│ - Output: plan.md OR TodoWrite                                  │
└─────────────────────────────────────────────────────────────────┘
```

## Round Prompts

### Round 1: Initial Perspective

```yaml
prompt_template: |
  You are the {role} in a multi-agent brainstorm.

  **Topic**: {topic}

  **Your perspective**: {perspective_description}

  Provide your initial analysis:
  1. Key observations from your perspective
  2. Main concerns or opportunities
  3. Initial recommendation

  Be concise but substantive. You will see others' views in the next round.
```

### Round 2: Debate

```yaml
prompt_template: |
  You are the {role} in a multi-agent brainstorm.

  **Topic**: {topic}

  **Round 1 Results**:

  Critic said:
  {critic_round1}

  Creative said:
  {creative_round1}

  Pragmatist said:
  {pragmatist_round1}

  **Your task**:
  1. Respond to points you disagree with (be specific)
  2. Acknowledge valid points from others
  3. Refine your position based on new information

  Be direct. Constructive conflict leads to better outcomes.
```

### Round 3: Final Position

```yaml
prompt_template: |
  You are the {role}. This is the final round.

  **Topic**: {topic}

  **Full debate history**:
  {all_rounds}

  **Your final position**:
  1. What changed in your thinking?
  2. What remains non-negotiable?
  3. Your concrete recommendation

  Be decisive. The coordinator will synthesize all positions.
```

## Execution Steps

### Step 1: Parse Input

```python
# Extract topic and any constraints
topic = user_input.strip()
output_format = detect_output_preference(topic)  # "plan" | "todos" | "document"
```

### Step 2: Round 1 - Parallel Agent Calls

```yaml
# Call all 3 agents in parallel
Task(subagent_type="noin-ai:gpt52-reviewer", prompt=round1_critic_prompt)
Task(subagent_type="noin-ai:gemini-designer", prompt=round1_creative_prompt)
Task(subagent_type="noin-ai:codex-max-coder", prompt=round1_pragmatist_prompt)
```

### Step 3: Collect & Prepare Round 2

```python
# Opus collects results
round1_results = {
    "critic": critic_response,
    "creative": creative_response,
    "pragmatist": pragmatist_response
}

# Identify key disagreements
disagreements = analyze_disagreements(round1_results)

# Prepare round 2 prompts with cross-references
```

### Step 4: Round 2 - Parallel Debate

```yaml
# Each agent sees others' Round 1 output
Task(subagent_type="noin-ai:gpt52-reviewer", prompt=round2_critic_prompt)
Task(subagent_type="noin-ai:gemini-designer", prompt=round2_creative_prompt)
Task(subagent_type="noin-ai:codex-max-coder", prompt=round2_pragmatist_prompt)
```

### Step 5: Synthesis

```yaml
# Opus synthesizes all perspectives
output:
  - consensus_points: [...]
  - remaining_debates: [...]
  - recommendations: [...]
  - action_items: [...]  # For TodoWrite
```

## Output Formats

### Option 1: Discussion Summary (Default)

```markdown
## Brainstorm: {topic}

### Consensus
- Point 1: All agents agree...
- Point 2: Unanimous recommendation...

### Debates
| Topic | Critic | Creative | Pragmatist |
|-------|--------|----------|------------|
| X     | Against | For      | Neutral    |
| Y     | For     | For      | Against    |

### Recommendations
1. **Do this first**: ...
2. **Consider carefully**: ...
3. **Avoid**: ...

### Next Steps
- [ ] Action item 1
- [ ] Action item 2
```

### Option 2: Plan Document

```markdown
# Implementation Plan: {topic}

Based on multi-agent brainstorm (3 rounds, 3 perspectives).

## Approach
...

## Phases
...

## Risks & Mitigations
...
```

### Option 3: TodoWrite

```yaml
todos:
  - content: "Research option A (recommended by Pragmatist)"
    status: pending
  - content: "Prototype UX flow (Creative's suggestion)"
    status: pending
  - content: "Security review for approach B (Critic's concern)"
    status: pending
```

## Configuration

### Default Settings

```yaml
brainstorm:
  rounds: 2  # 2-3 rounds typically sufficient
  agents:
    - role: critic
      subagent: "noin-ai:gpt52-reviewer"
    - role: creative
      subagent: "noin-ai:gemini-designer"
    - role: pragmatist
      subagent: "noin-ai:codex-max-coder"
  output: "summary"  # summary | plan | todos
  parallel: true  # Run agents in parallel within each round
```

### Custom Configuration

```yaml
# For architecture decisions
brainstorm:
  rounds: 3
  focus: "architecture"
  output: "plan"

# For quick ideation
brainstorm:
  rounds: 2
  focus: "ideas"
  output: "summary"
```

## Examples

### Example 1: Technology Choice

```
User: /brainstorm 应该用 Redis 还是 PostgreSQL 做 session 存储?

Round 1:
- Critic: "Redis 有持久化风险，PostgreSQL 更可靠..."
- Creative: "Redis 的速度能提升用户体验..."
- Pragmatist: "团队更熟悉 PostgreSQL，学习成本..."

Round 2:
- Critic: "同意 Pragmatist 的团队因素，但 Redis 集群..."
- Creative: "针对 Critic 的可靠性担忧，可以用 Redis + 备份..."
- Pragmatist: "Creative 的方案增加运维复杂度..."

Synthesis:
- Consensus: 都同意需要考虑团队经验
- Debate: Redis 性能 vs PostgreSQL 简单性
- Recommendation: 先用 PostgreSQL，后期按需迁移 Redis
```

### Example 2: Feature Design

```
User: /brainstorm 如何设计一个用户反馈系统?

Round 1:
- Critic: "需要防止滥用和垃圾反馈..."
- Creative: "可以用 emoji 反应降低提交门槛..."
- Pragmatist: "MVP 只需要文本框 + 分类..."

Round 2:
- Critic: "emoji 好，但需要 rate limiting..."
- Creative: "同意 MVP 简单化，但要预留扩展..."
- Pragmatist: "Critic 的 rate limiting 可以用现有中间件..."

Synthesis:
- Phase 1: 简单文本 + 分类 (Pragmatist)
- Phase 2: 添加 emoji 快捷反馈 (Creative)
- 全程: Rate limiting (Critic)
```

## Error Handling

### Agent Timeout

```yaml
on_timeout:
  - Log which agent timed out
  - Continue with available responses
  - Note incomplete perspective in output
```

### Consensus Too Early

```yaml
if all_agents_agree_round1:
  - Skip additional rounds
  - Output consensus directly
  - Note: "Unanimous agreement reached in Round 1"
```

### No Useful Disagreement

```yaml
if disagreements_trivial:
  - Reduce to 2 rounds
  - Focus synthesis on action items
  - Avoid artificial debate
```

## Integration

### With /code

```
/brainstorm 认证方案选择
→ Recommendation: JWT + Redis session hybrid
→ User: "好，按这个方案实现"
/code implement JWT auth with Redis session fallback
```

### With /plan

```
/brainstorm 如何重构支付模块
→ Output: plan.md with phases
→ User reviews and approves
→ Execute plan
```
