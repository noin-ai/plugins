# noin-plugins

Multi-agent Claude Code plugin leveraging specialized AI models for different tasks.

## Agents

| Agent | Model | Purpose |
|-------|-------|---------|
| `codex-coder` | codex | Standard code generation |
| `codex-max-coder` | codex-max | Complex/security-critical code |
| `gpt52-reviewer` | gpt-5.2 | Thorough code review |
| `gemini-designer` | gemini | UI/UX design |

## Commands

| Command | Description |
|---------|-------------|
| `/code <task>` | Generate code using Codex |
| `/code --complex <task>` | Use Codex-max for complex tasks |
| `/review [target]` | Code review with GPT-5.2 |
| `/design <component>` | UI design with Gemini |
| `/design --implement <component>` | Design + generate code |

## Installation

```bash
# Clone the repository
git clone https://github.com/liubiao/noin-plugins.git

# Install as Claude Code plugin
claude plugins install ./noin-plugins
```

## Architecture

```
Opus (main window)
├── Routes tasks to specialized agents
├── Orchestrates multi-agent workflows
└── Makes quick decisions

Codex / Codex-max
├── Code generation
├── Feature implementation
└── Refactoring

GPT-5.2
├── Code review
├── Bug detection
└── Security analysis

Gemini
├── UI/UX design
├── Component layouts
└── Design system
```

## Workflow Example

```
User: "Build a login form"
     ↓
Opus: Analyze → route to Gemini for design
     ↓
Gemini: Create design spec
     ↓
Opus: Route to Codex for implementation
     ↓
Codex: Generate component code
     ↓
Opus: Route to GPT-5.2 for review
     ↓
GPT-5.2: Review and approve
     ↓
Opus: Return final result
```

## License

MIT
