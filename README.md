# Framework Developer Orchestrator v4.0.0

A Claude Code plugin that helps you plan and finalize software project frameworks through structured discussion, research-backed recommendations, engineering rigor, and multi-agent coordination.

## What's New in v4.0.0

- **Premise Challenge** - Validates the "why" before building the "what"
- **4 Scope Modes** - Expansion, Selective, Hold, Reduction — choose your planning posture
- **Implementation Alternatives** - 2-3 approaches evaluated before committing (mandatory)
- **Dream State Mapping** - Current → Plan → 12-month ideal trajectory
- **0-10 Rating Method** - Rate, identify gap, fix, re-rate for every dimension
- **Scope Challenge** - Complexity check, search check, completeness check, distribution check
- **Outside Voice** - Independent AI second opinion via subagent
- **Confidence Scoring** - Every finding scored 1-10 with calibrated display rules
- **Interactive Review** - One issue per AskUserQuestion, STOP between sections
- **ASCII Coverage Diagrams** - Visual test coverage mapping with gap analysis
- **Review Report** - Standardized findings summary with deferred items
- **Autoplan Command** - `/autoplan` for fully auto-reviewed plans via 6 decision principles
- **Prime Directives** - 9 non-negotiable engineering principles
- **Cognitive Patterns** - 17 thinking instincts from CEO, engineering, and design disciplines
- **New `scope-review` Skill** - Dedicated skill for scope challenge and mode selection

## Features

- **6-Phase Structured Workflow** - From discovery to integration with engineering rigor
- **Research-First Approach** - Every recommendation backed by sources with confidence scores
- **Multi-Agent Orchestration** - Coordinate multiple LLMs for complex projects
- **Context Hygiene ("Small Circle")** - Prevents context drift in long sessions
- **ASCII + Mermaid Diagrams** - Auto-generated architecture visualizations
- **State Persistence** - Save and resume project planning sessions
- **Blueprint Generation** - Comprehensive project documentation
- **Scope Modes** - 4 planning postures for different project needs
- **Outside Voice** - Independent plan challenge from a fresh AI perspective

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
| `/autoplan` | **NEW** Auto-review pipeline — rough idea in, fully reviewed plan out |
| `/checkpoint` | Force a context refresh and state snapshot |
| `/validate [contracts\|links\|state\|all]` | Validate blueprints against implementation |
| `/rollback [list\|phase-N\|checkpoint-ID]` | Rollback to a previous checkpoint |
| `/export [pdf\|html\|notion\|markdown]` | Export blueprints to shareable formats |

## Workflow Phases

| Phase | Name | Purpose | Skills Used |
|-------|------|---------|-------------|
| 1 | **Discovery** | Premise challenge, scope mode, alternatives, dream state, outline | scope-review, research-workflow |
| 2 | **Structure** | Module hierarchy (rated 0-10), interaction states, dependencies | architecture-patterns, architecture-decision-records |
| 3 | **Planning** | Scope challenge, API contracts, coding sequence | scope-review, api-design-principles, openapi-spec-generation |
| 3.5 | **Outside Voice** | Independent AI second opinion on the plan | (Agent subagent) |
| 4 | **Agents** | Research-backed LLM assignments, tailored prompts | llm-capability-matching |
| 5 | **Execution** | Test coverage diagrams, handoffs, quality gates | handoff-protocol, deployment-pipeline-design |
| 6 | **Integration** | Review report, final blueprint, deployment readiness | blueprint-generation, e2e-testing-patterns |

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
| `scope-review` | **NEW** Scope challenge, premise validation, 4 scope modes |

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
2. **PreToolUse Hook** - Re-reads critical files before every write
3. **PostToolUse Hook** - Verifies work matches contracts
4. **State Snapshots** - `state-summary-phase-N.md` files survive context compaction

### Research-First

The agent NEVER assumes or hallucinates. Every recommendation is:
- Researched using WebSearch/WebFetch
- Backed by credible sources with URLs
- Presented with pros/cons for user decision
- Scored with confidence (1-10)

### Scope Modes (New in v4)

Choose your planning posture at the start:
- **EXPANSION** — Dream big. 10x thinking. Push scope up where 2x effort yields 10x value.
- **SELECTIVE EXPANSION** — Hold scope, but surface every expansion opportunity individually.
- **HOLD SCOPE** — Make what you described bulletproof. No expansion, no reduction.
- **SCOPE REDUCTION** — Find the minimum viable version. Cut everything non-essential.

### Autoplan (New in v4)

`/autoplan` runs the full 6-phase workflow with auto-decisions using 6 principles:
1. Choose completeness
2. Boil lakes (fix blast radius)
3. Pragmatic (cleaner wins)
4. DRY (reuse what exists)
5. Explicit over clever
6. Bias toward action

Only premises and user challenges require human input. Everything else is auto-decided with a full audit trail.

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
├── 00-project-state.json          # Master state (see state-schema.json)
├── 01-discovery/
│   ├── outline-v1.md              # Initial high-level outline
│   ├── outline-detailed.md        # Expanded bullet points
│   ├── architecture-diagram.md    # Mermaid + ASCII diagrams
│   ├── decisions-log.md           # Decisions with sources
│   ├── premise-challenge.md       # Premise validation
│   ├── dream-state.md             # Current → Plan → 12-month ideal
│   └── alternatives.md            # Implementation approaches considered
├── 02-structure/
│   ├── module-hierarchy.md        # Complete module breakdown
│   ├── dependency-graph.md        # Module relationships
│   ├── risk-assessment.md         # Risks with confidence scores
│   ├── verbal-links.md            # Connections in plain language
│   └── interaction-states.md      # Loading/empty/error/success/partial
├── 03-api-planning/
│   ├── api-contracts.md           # All endpoints (SOURCE OF TRUTH)
│   ├── call-signs.md              # Verified module connections
│   ├── coding-sequence.md         # Task order with dependencies
│   └── scope-challenge.md         # Complexity/search/completeness checks
├── 04-agent-assignment/
│   ├── work-division.md           # How work is split
│   ├── llm-capabilities.md        # Research on each LLM
│   ├── assignment-matrix.md       # Which LLM → which tasks
│   └── prompts/                   # Tailored prompts for each agent
├── 05-execution/
│   ├── handoff-protocol.md        # Standard handoff format
│   ├── progress-tracker.md        # Status dashboard
│   ├── shared-context.md          # Context all agents need
│   ├── test-coverage-diagram.md   # ASCII coverage map
│   ├── state-summary-phase-5.md   # 50% checkpoint summary
│   └── handoffs/                  # Individual handoff files
├── 06-integration/
│   ├── integration-checklist.md   # All verification checks
│   ├── conflict-resolution.md     # Conflicts and resolutions
│   ├── test-plan.md               # Integration tests
│   ├── final-report.md            # Complete project documentation
│   └── review-report.md           # Findings summary with deferred items
└── backups/                       # State file backups
```

## Plugin Structure

```
framework-developer-agent/
├── .claude-plugin/
│   ├── plugin.json              # Plugin manifest (v4.0.0)
│   └── marketplace.json         # Marketplace metadata
├── .claude/agents/
│   └── framework-developer.md   # Agent definition
├── commands/
│   ├── framework-dev.md         # Main orchestrator command
│   ├── autoplan.md              # Auto-review pipeline
│   ├── checkpoint.md            # Context refresh command
│   ├── validate.md              # Blueprint validation
│   ├── rollback.md              # Checkpoint recovery
│   └── export.md                # Export to PDF/HTML/Notion
├── hooks/
│   └── hooks.json               # 5 automated hooks
├── skills/                      # 12 skills for workflow phases
├── LICENSE
└── README.md
```

## Requirements

- [Claude Code](https://claude.com/claude-code) CLI installed
- Active Claude subscription
- Git (recommended for checkpoints and rollback)
- Pandoc (optional, for PDF export)

## Changelog

### v4.0.0
- Added premise challenge (never auto-decided, validates the "why")
- Added 4 scope modes (Expansion, Selective, Hold, Reduction)
- Added mandatory implementation alternatives (2-3 approaches)
- Added dream state mapping (Current → Plan → 12-month ideal)
- Added 0-10 rating method for structure review
- Added scope challenge (complexity, search, completeness, distribution checks)
- Added outside voice (Phase 3.5 — independent AI second opinion)
- Added confidence scoring (1-10 on every finding)
- Added interactive review pattern (one issue per question, STOP between sections)
- Added ASCII test coverage diagrams with gap analysis
- Added review report (standardized findings summary)
- Added `/autoplan` command (auto-review with 6 decision principles)
- Added `scope-review` skill
- Added prime directives (9 non-negotiable engineering principles)
- Added 17 cognitive patterns (CEO, engineering, design instincts)
- Added interaction state table (loading/empty/error/success/partial)
- Enhanced hooks with scope mode enforcement and link verification
- Enhanced system audit (git log, diff, TODOS, CLAUDE.md)

### v3.0.0
- Added 9 missing skills for complete workflow coverage
- Added `/validate`, `/rollback`, `/export` commands
- Git-integrated hooks with auto-checkpointing

- Blueprint validation system
- Export to PDF/HTML/Notion/Markdown

### v2.1.0
- Added "Small Circle" context hygiene strategy
- PreToolUse/PostToolUse hooks for context sync
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
