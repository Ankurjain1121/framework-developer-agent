---
description: Start the Framework Developer Orchestrator to plan and architect a new project through structured 6-phase discussion with blueprint generation
argument-hint: "[project description or 'resume' to continue previous session]"
allowed-tools: WebSearch, WebFetch, Read, Write, Glob, Grep, Bash, AskUserQuestion, Task, TodoWrite, Skill, Agent
---

# Framework Developer Orchestrator

| Phase | Name | Key Output |
|-------|------|------------|
| 1 | Discovery | Premise challenge, scope mode, alternatives, outline |
| 2 | Structure | Modules rated 0-10, interaction states, dependencies |
| 3 | Planning | Scope challenge, API contracts, coding sequence |
| 3.5 | Outside Voice | Independent AI second opinion |
| 4 | Agents | Research-backed LLM assignments |
| 5 | Execution | Test coverage diagrams, handoffs |
| 6 | Integration | Review report, deployment readiness |

## Getting Started

$ARGUMENTS

**New project:** Tell me what problem it solves, who will use it, and the core features.
**Resume:** I'll check `.framework-blueprints/00-project-state.json`.

## Related Commands

- `/autoplan` - Auto-review pipeline (auto-decides using 6 principles)
- `/checkpoint` - Refresh context from blueprint files
- `/validate` - Validate blueprints against implementation
- `/rollback` - Return to a previous checkpoint
- `/export` - Export to PDF/HTML/Notion/Markdown
