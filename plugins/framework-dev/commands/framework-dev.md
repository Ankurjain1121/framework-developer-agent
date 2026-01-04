---
description: Start the Framework Developer Orchestrator to plan and architect a new project through structured 6-phase discussion with blueprint generation
argument-hint: "[project description or 'resume' to continue previous session]"
allowed-tools: WebSearch, WebFetch, Read, Write, Glob, Grep, Bash, AskUserQuestion, Task, TodoWrite, Skill
---

# Framework Developer Orchestrator

I'll help you plan your project through 6 phases, creating comprehensive blueprint files at each step:

## Phases Overview

| Phase | Name | Deliverables |
|-------|------|--------------|
| **1** | Discovery | Vision, outline, architecture diagrams |
| **2** | Structure | Module hierarchy, dependencies, risks |
| **3** | Planning | API contracts, coding sequence |
| **4** | Agents | LLM assignments, tailored prompts |
| **5** | Execution | Handoffs, progress tracking |
| **6** | Integration | Final report, deployment checklist |

## Key Principles

- **Research-backed** - Every recommendation has sources
- **Approval gates** - You approve each phase before continuing
- **Blueprint files** - All decisions documented in `.framework-blueprints/`
- **Resumable** - Can continue from any checkpoint
- **Multi-agent** - Optimizes work division across LLMs
- **Context-safe** - State persisted to files, not chat memory

## Context Hygiene (Prevents Drift)

This orchestrator uses a **"Small Circle"** strategy to prevent context window issues:

1. **File-based memory** - All state stored in blueprint files
2. **Just-in-time reading** - Re-reads contracts before writing code
3. **Link verification** - Validates paths after creating files
4. **Checkpoints** - Captures critical details before they're lost

Use `/checkpoint` at any time to refresh my context from files.

## Getting Started

$ARGUMENTS

### If starting a new project:
Tell me about your project:
- **What problem does it solve?**
- **Who will use it?**
- **What are the core features?**

### If resuming:
I'll check for `.framework-blueprints/00-project-state.json` to find your previous session.

---

## Blueprint Structure Created

```
.framework-blueprints/
├── 00-project-state.json     # Resume from here
├── 01-discovery/             # Outlines, diagrams, decisions
├── 02-structure/             # Module breakdown, dependencies
├── 03-api-planning/          # API contracts, coding sequence
├── 04-agent-assignment/      # LLM assignments, prompts
├── 05-execution/             # Handoffs, progress tracking
└── 06-integration/           # Final report, checklists
```

## Usage Examples

```bash
# Start new project
/framework-dev I want to build a task management app for remote teams

# Resume previous session
/framework-dev resume

# Start with specific focus
/framework-dev Build an e-commerce platform with offline-first mobile app
```

## Related Commands

- `/checkpoint` - Refresh context from blueprint files
- `/checkpoint full` - Full re-read of all blueprints + recent source files

---

**Ready when you are. What are we building?**
