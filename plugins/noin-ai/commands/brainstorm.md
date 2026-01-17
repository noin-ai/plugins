---
description: Multi-agent brainstorming with Critic, Creative, and Pragmatist perspectives
argument-hint: <topic> [--rounds=2] [--output=summary]
allowed-tools:
  - Task
  - TodoWrite
---

Launch a multi-agent brainstorming session where three AI agents with different perspectives discuss a problem.

## Agent Roles

| Agent | Role | Focus |
|-------|------|-------|
| GPT-5.2 | Critic | Quality, security, risks |
| Gemini | Creative | User experience, innovation |
| Codex-Max | Pragmatist | Implementation, cost |

## Usage

```
/brainstorm Should we use Redis or PostgreSQL for session storage?
/brainstorm How to design a user feedback system? --rounds=3
/brainstorm Authentication approach --output=plan
```

## Arguments

- `topic`: The question or idea to brainstorm (required)
- `--rounds`: Number of debate rounds, 2-3 (default: 2)
- `--output`: Output format - summary, plan, or todos (default: summary)

## Execution

Invoke the brainstorm skill to orchestrate the multi-agent debate:

```
Skill: brainstorm
Args: $ARGUMENTS
```
