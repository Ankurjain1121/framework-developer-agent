# Framework Developer Orchestrator v3.0.0

A Claude Code plugin that helps you plan and finalize software project frameworks through structured discussion, research-backed recommendations, and multi-agent coordination.

## What's New in v3.0.0

- **Git-Integrated Checkpoints** - Auto-commit and tag at phase transitions
- **Validation System** - Verify blueprints against implementation (`/validate`)
- **Rollback Support** - Return to any checkpoint (`/rollback`)
- **Export Capabilities** - PDF, HTML, Notion, Markdown (`/export`)
- **9 New Skills** - Complete skill set for all workflow phases
- **Enhanced Hooks** - 8 automated hooks with Git integration

## Features

- **6-Phase Structured Workflow** - From discovery to integration
- **Research-First Approach** - Every recommendation backed by sources
- **Multi-Agent Orchestration** - Coordinate multiple LLMs for complex projects
- **Context Hygiene ("Small Circle")** - Prevents context drift in long sessions
- **Mermaid Diagrams** - Auto-generated architecture visualizations
- **State Persistence** - Save and resume project planning sessions
- **Blueprint Generation** - Comprehensive project documentation

## Installation

### Option 1: Via GitHub (Recommended)

Add to your `~/.claude/settings.json`:

```json
{
  "extraKnownMarketplaces": {
    "framework-dev": {
      "source": {
        "source": "github",
        "repo": "Ankurjain1121/framework-developer-agent"
      }
    }
  },
  "enabledPlugins": {
    "framework-dev@framework-dev": true
  }
}
```

### Option 2: Local Clone

```bash
git clone https://github.com/Ankurjain1121/framework-developer-agent.git ~/.claude/plugins/framework-dev
```

### Option 3: CLI with --plugin-dir

```bash
claude --plugin-dir /path/to/framework-developer-agent
```

## Usage

### Start a New Project

```
/framework-dev
```

Or invoke through natural language:
```
"Help me plan the framework for my new project"
```

### Commands

| Command | Description |
|---------|-------------|
| `/framework-dev` | Start the orchestrator for a new or existing project |
| `/checkpoint` | Force a context refresh and state snapshot |
| `/validate [contracts\|links\|state\|all]` | Validate blueprints against implementation |
| `/rollback [list\|phase-N\|checkpoint-ID]` | Rollback to a previous checkpoint |
| `/export [pdf\|html\|notion\|markdown]` | Export blueprints to shareable formats |

## Workflow Phases

| Phase | Name | Purpose | Skills Used |
|-------|------|---------|-------------|
| 1 | **Discovery** | Understand project vision, target users, major modules | research-workflow |
| 2 | **Structure** | Break down into sub-components, map dependencies, identify risks | architecture-patterns, architecture-decision-records |
| 3 | **Planning** | Define API contracts, coding tasks, implementation sequence | api-design-principles, openapi-spec-generation |
| 4 | **Agents** | Research available LLMs and assign tasks based on capabilities | llm-capability-matching |
| 5 | **Execution** | Track progress, coordinate agents, handle handoffs | handoff-protocol, deployment-pipeline-design |
| 6 | **Integration** | Merge work, resolve conflicts, generate final report | blueprint-generation, e2e-testing-patterns |

## Skills

| Skill | Purpose |
|-------|---------|
| `api-design-principles` | Contract-first API design guidance |
| `architecture-patterns` | MVC, Hexagonal, Clean Architecture, Microservices |
| `architecture-decision-records` | ADR format and templates |
| `blueprint-generation` | Project blueprint file templates |
| `deployment-pipeline-design` | CI/CD pipeline patterns |
| `e2e-testing-patterns` | Testing pyramid, contract tests, multi-agent testing |
| `handoff-protocol` | Agent-to-agent handoff procedures |
| `llm-capability-matching` | Match tasks to optimal LLMs with cost estimation |
| `openapi-spec-generation` | Generate OpenAPI 3.1 specifications |
| `project-state-management` | State tracking with checkpoints |
| `research-workflow` | Standardized technology research process |

## Hooks (Automated)

| Hook | Trigger | Purpose |
|------|---------|---------|
| `SessionStart` | On session begin | Check for existing state, offer to resume |
| `UserPromptSubmit` | Before processing prompt | Validate request aligns with current phase |
| `PreToolUse` | Before Write/Edit | Verify API contracts before code changes |
| `PostToolUse` | After Write/Edit | Update state after blueprint modifications |
| `Stop` | When response completes | Remind about checkpoints and git commits |

## Key Principles

### Context Hygiene ("Small Circle")

Long Claude sessions can suffer from "context drift" - the model forgetting earlier decisions. This plugin uses the "Small Circle" strategy:

1. **Master Truth Files** - `outline-v1.md` and `api-contracts.md` are the source of truth
2. **PreTask Hook** - Re-reads critical files before every task
3. **PostTask Hook** - Verifies work matches contracts
4. **State Snapshots** - `state-summary-phase-N.md` files survive context compaction

### Research-First

The agent NEVER assumes or hallucinates. Every recommendation is:
- Researched using WebSearch/WebFetch
- Backed by credible sources with URLs
- Presented with pros/cons for user decision

### Multi-Agent Coordination

When you have multiple LLMs available (Claude, Qwen, Gemini, GPT-4, etc.):
1. Agent researches each LLM's capabilities from official sources
2. Presents findings with source URLs
3. Asks about your experience with each
4. Recommends task assignments based on research + your input
5. Generates tailored prompts for each agent

### Git Integration

The plugin integrates with Git for:
- **Auto-checkpoints** at phase transitions
- **Tagged milestones** for easy rollback
- **Commit suggestions** after significant changes
- **Recovery options** when errors occur

## Generated Outputs

The agent produces in `.framework-blueprints/`:

```
.framework-blueprints/
├── 00-project-state.json        # Current state and history
├── 01-discovery/
│   ├── outline-v1.md            # Project structure
│   └── architecture-diagram.md  # Mermaid diagrams
├── 02-structure/
│   ├── module-hierarchy.md      # Component breakdown
│   └── dependency-map.md        # Module dependencies
├── 03-api-planning/
│   ├── api-contracts.md         # Endpoint specifications
│   └── openapi.yaml             # OpenAPI 3.1 spec
├── 04-agents/
│   ├── agent-assignments.md     # Task-to-LLM mapping
│   └── agent-prompts/           # Tailored prompts
├── 05-execution/
│   ├── task-queue.md            # Ordered task list
│   └── handoffs/                # Handoff documents
├── 06-integration/
│   └── final-report.md          # Comprehensive documentation
├── backups/                     # State file backups
└── state-summary-phase-*.md     # Context-safe snapshots
```

## Plugin Structure

```
framework-developer-agent/
├── .claude-plugin/
│   └── plugin.json              # Plugin manifest (v3.0.0)
├── .claude/agents/
│   └── framework-developer.md   # Agent definition
├── commands/
│   ├── framework-dev.md         # Main orchestrator command
│   ├── checkpoint.md            # Context refresh command
│   ├── validate.md              # Blueprint validation
│   ├── rollback.md              # Checkpoint recovery
│   └── export.md                # Export to PDF/HTML/Notion
├── hooks/
│   └── hooks.json               # 8 automated hooks
├── skills/                      # 11 skills for workflow phases
├── plugins/framework-dev/       # Alternative plugin structure
├── LICENSE
└── README.md
```

## Requirements

- [Claude Code](https://claude.com/claude-code) CLI installed
- Active Claude subscription
- Git (recommended for checkpoints and rollback)
- Pandoc (optional, for PDF export)

## Changelog

### v3.0.0
- Added 9 missing skills for complete workflow coverage
- Added `/validate`, `/rollback`, `/export` commands
- Git-integrated hooks with auto-checkpointing
- New `HandoffComplete` and `BlueprintModified` hook events
- Blueprint validation system
- Export to PDF/HTML/Notion/Markdown

### v2.1.0
- Added "Small Circle" context hygiene strategy
- PreTask/PostTask hooks for context sync
- `/checkpoint` command for manual snapshots
- Mandatory state snapshots at phase transitions

### v2.0.0
- Initial 6-phase workflow
- Blueprint generation system
- Multi-agent orchestration

## License

MIT License - see [LICENSE](LICENSE) file

## Contributing

Contributions welcome! Please feel free to submit issues or pull requests.

See the [GitHub Issues](https://github.com/Ankurjain1121/framework-developer-agent/issues) for planned improvements.

## Author

Built by Ankur Jain with Claude Code
