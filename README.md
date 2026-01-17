# noin-ai

Multi-agent Claude Code plugin powered by [noin.ai](https://noin.ai).

[![GitHub](https://img.shields.io/badge/GitHub-noin--ai%2Fplugins-blue)](https://github.com/noin-ai/plugins)
[![License](https://img.shields.io/badge/License-MIT-green)](LICENSE)
[![中文文档](https://img.shields.io/badge/中文-README-orange)](README.zh-CN.md)

## What is noin.ai?

[noin.ai](https://noin.ai) is an API platform that provides access to multiple AI models. This plugin integrates these models into Claude Code, enabling multi-agent workflows without requiring additional API keys.

**Key Benefits:**
- Access to GPT-5.2, Codex, Gemini and more through a unified interface
- No additional API keys required - authentication handled by noin.ai
- Specialized agents for different tasks (code, review, design, brainstorm)

---

## Quickstart

### Installation

```bash
# Add marketplace and install
/plugin marketplace add noin-ai/plugins
/plugin install noin-ai
```

### Try It Now

```bash
# Generate code
/code write a function to validate email addresses

# Review current changes
/review

# Start a brainstorm
/brainstorm Should we use Redis or PostgreSQL for sessions?
```

**Success indicators:**
- Agent returns generated code or analysis
- No API key errors

---

## Agents

| Agent | Model | Purpose |
|-------|-------|---------|
| `coder` | Codex | Standard code generation |
| `coder-advanced` | Codex (advanced) | Complex/security-critical code |
| `reviewer` | GPT-5.2 | Code review, security analysis |
| `designer` | Gemini | UI/UX design |
| `browser-agent` | - | Web scraping, screenshots |
| `data-agent` | - | Data transformation, CSV/JSON |

---

## Skills

### Code Development

| Skill | Description |
|-------|-------------|
| `/code <task>` | Generate code |
| `/code --complex <task>` | Use coder-advanced for complex tasks |
| `/review` | Review staged changes |
| `/review <file>` | Review specific file |
| `/review --security` | Security-focused review |
| `/design <component>` | Create UI design spec |
| `/design --implement` | Design + generate code |
| `/simplify` | Simplify recently modified code |
| `/simplify <file>` | Simplify specific file |
| `/simplify --aggressive` | Allow more significant refactoring |

### Multi-Agent Brainstorm

| Skill | Description |
|-------|-------------|
| `/brainstorm <topic>` | 3 agents debate: Critic + Creative + Pragmatist |
| `/brainstorm --rounds 3` | Extended 3-round discussion |
| `/brainstorm --output plan` | Output as implementation plan |

### Workflows

| Skill | Description |
|-------|-------------|
| `/create-workflow` | Create custom workflow interactively |
| `/run-workflow <name>` | Execute a saved workflow |
| `/list-workflows` | List available workflows |

---

## Architecture

```
User Request
    ↓
Claude Opus (orchestrator)
    ├── Analyzes task complexity
    ├── Routes to specialized agents
    └── Integrates results
         ↓
    ┌────────────────────────────────────┐
    │ Code Agents                        │
    │ ├── coder                    │
    │ ├── coder-advanced                │
    │ ├── reviewer                 │
    │ └── designer                │
    ├────────────────────────────────────┤
    │ Brainstorm Mode                    │
    │ ├── Critic (reviewer)        │
    │ ├── Creative (designer)     │
    │ └── Pragmatist (coder-advanced)   │
    ├────────────────────────────────────┤
    │ Workflow Agents                    │
    │ ├── browser-agent                  │
    │ └── data-agent                     │
    └────────────────────────────────────┘
```

---

## Glossary

| Term | Definition |
|------|------------|
| **Agent** | A specialized AI worker that runs in a separate context with specific tools and model |
| **Skill** | Instructions that Claude reads and executes, accessible via `/skill-name` |
| **Workflow** | A YAML-defined sequence of agent actions |

---

## Custom Workflows

Create reusable automation workflows that chain multiple agents.

### Example: Web Scraping

```bash
# Create workflow
/create-workflow scrape website and convert to CSV

# Run it
/run-workflow scrape-prices --url "https://example.com"
```

### Workflow Storage

| Scope | Path |
|-------|------|
| Project | `.claude/noin-workflows/` |
| User | `~/.claude/noin-workflows/` |

---

## FAQ

**Q: Do I need an API key?**
A: No. The plugin uses noin.ai's hosted API. No additional configuration required.

**Q: Which models are available?**
A: GPT-5.2, Codex, Codex-max, Gemini. See [Agents](#agents) table.

**Q: How do I report issues?**
A: Open an issue at [github.com/noin-ai/plugins/issues](https://github.com/noin-ai/plugins/issues)

---

## Development

```bash
git clone https://github.com/noin-ai/plugins.git
cd plugins
/plugin install . --scope local
```

---

## License

MIT
