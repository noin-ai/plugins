---
description: Multi-agent brainstorming with Critic, Creative, and Pragmatist perspectives
argument-hint: <topic> [--rounds=2] [--output=summary]
allowed-tools:
  - Task
  - TodoWrite
  - AskUserQuestion
  - EnterPlanMode
---

Launch a multi-agent brainstorming session where three AI agents with different perspectives discuss a problem.

## Agent Roles

| Agent | Role | Focus |
|-------|------|-------|
| `noin-ai:gpt52-reviewer` | Critic | Quality, security, risks |
| `noin-ai:gemini-designer` | Creative | User experience, innovation |
| `noin-ai:codex-max-coder` | Pragmatist | Implementation, cost |

## Usage

```
/brainstorm Should we use Redis or PostgreSQL for session storage?
/brainstorm How to design a user feedback system? --rounds=3
/brainstorm Authentication approach --output=plan
```

## Execution

Follow the brainstorm skill instructions to orchestrate the multi-agent debate.

**Topic:** $ARGUMENTS
