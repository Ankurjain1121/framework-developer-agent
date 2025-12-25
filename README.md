# Framework Developer Orchestrator

A Claude Code agent that helps you plan and finalize software project frameworks through structured discussion, research-backed recommendations, and multi-agent coordination.

## Features

- **6-Phase Structured Workflow** - From discovery to integration
- **Research-First Approach** - Every recommendation backed by sources
- **Multi-Agent Orchestration** - Coordinate multiple LLMs for complex projects
- **Mermaid Diagrams** - Auto-generated architecture visualizations
- **State Persistence** - Save and resume project planning sessions

## Installation

### Option 1: Copy to your Claude Code agents folder

```bash
# Clone this repository
git clone https://github.com/Ankurjain1121/framework-developer-agent.git

# Copy the agent definition to your Claude Code agents folder
cp framework-developer-agent/.claude/agents/framework-developer.md ~/.claude/agents/
```

### Option 2: Manual installation

1. Create the agents directory if it doesn't exist:
   ```bash
   mkdir -p ~/.claude/agents
   ```

2. Copy the contents of `.claude/agents/framework-developer.md` to `~/.claude/agents/framework-developer.md`

## Usage

Once installed, you can invoke the agent in Claude Code using the Task tool:

```
"Help me plan the framework for my new project"
```

Or directly reference the agent when starting a new project discussion.

## Workflow Phases

| Phase | Name | Purpose |
|-------|------|---------|
| 1 | **Discovery** | Understand project vision, target users, major modules |
| 2 | **Structure** | Break down into sub-components, map dependencies, identify risks |
| 3 | **Planning** | Define API contracts, coding tasks, implementation sequence |
| 4 | **Agents** | Research available LLMs and assign tasks based on capabilities |
| 5 | **Execution** | Track progress, coordinate agents, handle handoffs |
| 6 | **Integration** | Merge work, resolve conflicts, generate final report |

## Key Principles

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

### State Management
The agent tracks:
- Current phase
- Decisions made (with sources)
- Modules and dependencies
- Risks identified
- Agent assignments

You can save/resume sessions at any checkpoint.

## Example Session

```
User: I want to build a task management app

Agent: Great! Let me understand your vision better.

**Question 1:** What specific problem does this task management app solve
that existing solutions (Todoist, Trello, etc.) don't address?

User: It's for teams that need offline-first capability

Agent: Interesting - offline-first for teams is a specific niche.

Let me research offline-first patterns...
[WebSearch: "offline-first architecture patterns 2025"]

Based on my research:
- **Conflict Resolution** is critical (Source: [URL])
- **CRDT patterns** are commonly used (Source: [URL])

**Question 2:** How large are the teams you're targeting?
...
```

## Generated Outputs

The agent produces:
- **Architecture diagrams** (Mermaid format)
- **API specifications** (endpoints, schemas)
- **Coding task sequences** (dependency-ordered)
- **Agent prompts** (tailored for each LLM)
- **Final project reports** (comprehensive documentation)

## Requirements

- [Claude Code](https://claude.com/claude-code) CLI installed
- Active Claude subscription

## License

MIT License - see [LICENSE](LICENSE) file

## Contributing

Contributions welcome! Please feel free to submit issues or pull requests.

## Author

Built with Claude Code
