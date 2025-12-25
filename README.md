# Framework Developer Plugin

A Claude Code plugin that provides a **Framework Developer Orchestrator** - an AI agent that helps plan and architect software projects through structured discussion.

## Features

- **6-Phase Workflow**: Discovery → Structure → Planning → Agent Assignment → Execution → Integration
- **Research-Backed**: Every recommendation includes source URLs
- **Multi-Agent Coordination**: Assign tasks across different LLMs (Claude, GPT-4, Gemini, etc.)
- **Visual Architecture**: Generates Mermaid diagrams for module relationships and data flow
- **State Management**: Save and resume project planning sessions

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

Then add to `~/.claude/settings.json`:

```json
{
  "extraKnownMarketplaces": {
    "framework-dev-local": {
      "source": {
        "source": "directory",
        "path": "~/.claude/plugins/framework-dev"
      }
    }
  },
  "enabledPlugins": {
    "framework-dev@framework-dev-local": true
  }
}
```

### Option 3: CLI with --plugin-dir

```bash
claude --plugin-dir /path/to/framework-developer-agent
```

## Usage

### Slash Command

```
/framework-dev [project description]
```

Example:
```
/framework-dev I want to build an offline-first task management app for teams
```

### Agent Invocation

The `framework-developer` agent can also be invoked via the Task tool for autonomous operation.

## Workflow Phases

| Phase | Goal | Output |
|-------|------|--------|
| 1. Discovery | Understand project vision | Vision statement, target users, modules |
| 2. Structure | Break down into components | Dependency graph, Mermaid diagrams |
| 3. Planning | Define implementation | API specs, coding sequence |
| 4. Agents | Assign work to LLMs | Agent table, tailored prompts |
| 5. Execution | Coordinate work | Progress dashboard, handoffs |
| 6. Integration | Merge and finalize | Final report, architecture docs |

## Key Principles

1. **Never Assume** - Research everything, cite sources
2. **Present Options** - User makes final decisions
3. **One Question at a Time** - Avoid overwhelming
4. **Confirm Understanding** - Summarize before proceeding

## Plugin Structure

```
framework-developer-agent/
├── .claude-plugin/
│   └── plugin.json          # Plugin manifest
├── agents/
│   └── framework-developer.md   # Agent definition
├── commands/
│   └── framework-dev.md     # Slash command
├── LICENSE
└── README.md
```

## License

MIT
