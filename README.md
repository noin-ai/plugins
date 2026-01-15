# noin-plugins

Multi-agent Claude Code plugin leveraging specialized AI models for different tasks.

🔗 **[noin.ai](https://noin.ai)** | 📦 [GitHub](https://github.com/noin-ai/plugins)

> **No additional API keys required** - noinai has adapted these models for this plugin.

## Installation

```bash
# Add marketplace source
/plugin marketplace add noin-ai/plugins

# Install the plugin
/plugin install noin-plugins
```

### Local Development

```bash
git clone https://github.com/noin-ai/plugins.git
/plugin install ./plugins --scope local
```

## Agents

### Code & Review Agents

| Agent | Model | Purpose |
|-------|-------|---------|
| `codex-coder` | codex | Standard code generation |
| `codex-max-coder` | codex-max | Complex/security-critical code |
| `gpt52-reviewer` | gpt-5.2 | Thorough code review |
| `gemini-designer` | gemini | UI/UX design |

### Workflow Agents

| Agent | Purpose |
|-------|---------|
| `browser-agent` | Web scraping, form interaction, screenshots |
| `data-agent` | Data transformation, CSV/JSON conversion, filtering |

## Commands

### Code Development

| Command | Description |
|---------|-------------|
| `/code <task>` | Generate code using Codex |
| `/code --complex <task>` | Use Codex-max for complex tasks |
| `/review [target]` | Code review with GPT-5.2 |
| `/review --security` | Security-focused review |
| `/review --accessibility` | Accessibility-focused review |
| `/design <component>` | UI design with Gemini |
| `/design --implement <component>` | Design + generate code |

### Multi-Agent Brainstorm

| Command | Description |
|---------|-------------|
| `/brainstorm <topic>` | Multi-agent debate (Critic + Creative + Pragmatist) |
| `/brainstorm <topic> --rounds 3` | Extended 3-round discussion |
| `/brainstorm <topic> --output plan` | Output as implementation plan |

### Custom Workflows

| Command | Description |
|---------|-------------|
| `/create-workflow <description>` | Interactive workflow creation |
| `/run-workflow <name>` | Execute a custom workflow |
| `/list-workflows` | List available workflows |

## Architecture

```
User Request
    ↓
Opus (main window - orchestrator)
    ├── Analyzes task complexity
    ├── Routes to specialized agents
    └── Integrates results

Code Agents
    ├── Codex / Codex-max: Code generation
    ├── GPT-5.2: Code review, security analysis
    └── Gemini: UI/UX design

Multi-Agent Brainstorm
    ├── Critic (GPT-5.2): Quality, risks, edge cases
    ├── Creative (Gemini): UX, elegance, innovation
    └── Pragmatist (Codex-max): Feasibility, cost, effort

Workflow Agents
    ├── browser-agent: Web scraping, automation
    └── data-agent: Data processing, transformation
```

## Custom Workflows

Create reusable automation workflows that chain multiple agents together.

### Example: Web Scraping to CSV

```bash
# Create workflow interactively
/create-workflow 抓取网页价格生成CSV

# Run the workflow
/run-workflow scrape-prices --url "https://example.com"
```

### Workflow Definition (YAML)

```yaml
name: scrape-prices
description: Scrape prices and generate CSV

inputs:
  - name: url
    required: true

steps:
  - id: fetch
    agent: browser-agent
    action: scrape
    inputs:
      url: ${{ inputs.url }}
      selectors:
        - name: product
          selector: ".product-name"
        - name: price
          selector: ".price"

  - id: convert
    agent: data-agent
    action: to_csv
    inputs:
      data: ${{ steps.fetch.outputs.data }}

  - id: save
    agent: file-agent
    action: write
    inputs:
      path: ./output/prices.csv
      content: ${{ steps.convert.outputs.content }}
```

### Workflow Storage

| Scope | Path | Usage |
|-------|------|-------|
| Project | `.claude/noin-workflows/` | Project-specific, version controlled |
| User | `~/.claude/noin-workflows/` | Personal, cross-project |

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

### Web Scraping Workflow

```
User: /run-workflow scrape-prices --url "https://ai.ourines.com"

1. workflow-executor: Load workflow definition
2. browser-agent: Scrape webpage
3. data-agent: Transform to CSV
4. file-agent: Save to file
5. Return: File path and summary
```

## Skills

Skills define when and how to route tasks to agents:

| Skill | Triggers | Agent(s) |
|-------|----------|----------|
| `code-generation` | write, implement, create, fix | codex-coder / codex-max-coder |
| `code-review` | review, check, audit, security | gpt52-reviewer |
| `ui-design` | design, UI, layout, component | gemini-designer |
| `brainstorm` | brainstorm, debate, perspectives | gpt52 + gemini + codex-max |
| `workflow-executor` | run-workflow, execute workflow | Multiple agents |

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
