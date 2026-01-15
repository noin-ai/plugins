# noin-plugins

Multi-agent Claude Code plugin leveraging specialized AI models for different tasks.

## Installation

```bash
# Add the marketplace (first time only)
/plugin marketplace add noin-ai/plugins

# Install the plugin
/plugin install noin-plugins@noin-ai/plugins

# Or install directly from GitHub
claude plugin install github:noin-ai/plugins
```

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
| `/review --security` | Security-focused review |
| `/review --accessibility` | Accessibility-focused review |
| `/design <component>` | UI design with Gemini |
| `/design --implement <component>` | Design + generate code |

## Architecture

```
User Request
    ↓
Opus (main window - orchestrator)
    ├── Analyzes task complexity
    ├── Routes to specialized agents
    └── Integrates results

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

## Workflow Examples

### Complete Component Development

```
User: /design --implement user login form

1. Opus → gemini-designer: Create design spec
2. Gemini: Output layout, styles, interactions, accessibility
3. Opus → codex-coder: Implement from spec
4. Codex: Generate component code
5. Opus → gpt52-reviewer: Accessibility review
6. GPT-5.2: Verify WCAG compliance
7. Opus: Return design + code + review
```

### Code Generation with Auto-Review

```
User: /code --complex implement payment processing

1. Opus: Detect complex/security-sensitive task
2. Opus → codex-max-coder: Generate secure code
3. Codex-max: Implement with security considerations
4. Opus → gpt52-reviewer: Security audit (auto-triggered)
5. GPT-5.2: OWASP check, auth review
6. Opus: Return code + security review
```

### Pre-Commit Review

```
User: /review --quick

1. Opus: Get staged git changes
2. Opus → gpt52-reviewer: Quick review
3. GPT-5.2: Check critical issues only
4. Opus: Return APPROVE or REQUEST_CHANGES
```

## Skills

Skills define when and how to route tasks to agents:

| Skill | Triggers | Agent(s) |
|-------|----------|----------|
| `code-generation` | write, implement, create, fix | codex-coder / codex-max-coder |
| `code-review` | review, check, audit, security | gpt52-reviewer |
| `ui-design` | design, UI, layout, component | gemini-designer |

## Configuration

This plugin requires configured AI models:
- **OpenAI API**: For codex, codex-max, gpt-5.2 models
- **Google AI API**: For gemini model

Models should be configured via Claude Code's `/model` command.

## Development

```bash
# Clone the repository
git clone https://github.com/noin-ai/plugins.git
cd plugins

# Install locally for development
claude plugin install . --scope local
```

## License

MIT
