---
name: blueprint-generation
description: Create comprehensive project blueprints, documentation artifacts, and structured planning files. Use when asked about creating blueprints, generating documentation, project artifacts, planning files, phase outputs, or saving project state.
---

# Blueprint Generation Skill

This skill enables the framework-developer agent to create comprehensive, structured blueprint files at each phase of project planning. Blueprints serve as the persistent memory and documentation of all planning decisions.

## When to Use This Skill

Trigger this skill when the user asks about:
- Creating blueprints or documentation
- Saving project state
- Generating phase outputs
- Creating planning artifacts
- Documenting architecture decisions
- Setting up project structure
- Resuming a previous planning session

## Blueprint Directory Structure

All blueprints are stored in `.framework-blueprints/` at the project root:

```
project-name/
└── .framework-blueprints/
    ├── 00-project-state.json          # Current state, resume from here
    ├── 01-discovery/
    │   ├── outline-v1.md              # Initial high-level outline
    │   ├── outline-detailed.md        # Expanded bullet points
    │   ├── architecture-diagram.md    # Mermaid charts
    │   └── decisions-log.md           # Decisions with sources
    ├── 02-structure/
    │   ├── module-hierarchy.md        # Complete module breakdown
    │   ├── dependency-graph.md        # Module relationships
    │   ├── risk-assessment.md         # Identified risks
    │   └── verbal-links.md            # All connections explained
    ├── 03-api-planning/
    │   ├── api-contracts.md           # All endpoints, schemas
    │   ├── call-signs.md              # Verified links between modules
    │   └── coding-sequence.md         # Task order with dependencies
    ├── 04-agent-assignment/
    │   ├── work-division.md           # How work is split
    │   ├── llm-capabilities.md        # Research on each LLM
    │   ├── assignment-matrix.md       # Which LLM -> which tasks
    │   └── prompts/
    │       └── [agent-name].md        # Tailored prompts for each agent
    ├── 05-execution/
    │   ├── handoff-protocol.md        # Standard handoff format
    │   ├── progress-tracker.md        # Status dashboard
    │   ├── shared-context.md          # Context all agents need
    │   └── handoffs/
    │       └── handoff-NNN.md         # Individual handoff files
    └── 06-integration/
        ├── integration-checklist.md   # All verification checks
        ├── conflict-resolution.md     # Conflicts and resolutions
        ├── test-plan.md               # Integration tests
        └── final-report.md            # Complete project blueprint
```

---

## Phase-by-Phase Blueprint Creation

### Phase 1: Discovery

**When to create:** After completing discovery discussions with user.

**Files to create:**

| File | Purpose | When |
|------|---------|------|
| `outline-v1.md` | Initial high-level outline | After first understanding of project |
| `outline-detailed.md` | Expanded with sub-points | After deeper discussion |
| `architecture-diagram.md` | Visual representation | Before Phase 1 checkpoint |
| `decisions-log.md` | All decisions with sources | Throughout Phase 1 |

**outline-v1.md contents:**
```markdown
# Project Outline v1

## Project Name
[Name]

## Problem Statement
[What problem does this solve?]

## Target Users
[Who will use this?]

## Core Modules
1. [Module A] - [one-line purpose]
2. [Module B] - [one-line purpose]
...

## Initial Relationships
- Module A -> Module B: [relationship]
```

**outline-detailed.md contents:**
```markdown
# Detailed Project Outline

## Module: [Name]

### Purpose
[Detailed explanation]

### Sub-components
- [Component 1]: [purpose]
- [Component 2]: [purpose]

### Data Handled
- [Data type 1]
- [Data type 2]

### Dependencies
- Requires: [other modules]
- Provides: [to other modules]
```

---

### Phase 2: Structure

**When to create:** After breaking down modules into detailed hierarchy.

**Files to create:**

| File | Purpose | When |
|------|---------|------|
| `module-hierarchy.md` | Complete tree structure | After module breakdown |
| `dependency-graph.md` | Mermaid dependency diagram | After relationships defined |
| `risk-assessment.md` | Identified risks and mitigations | Before Phase 2 checkpoint |
| `verbal-links.md` | Plain-language connections | Throughout Phase 2 |

**module-hierarchy.md contents:**
```markdown
# Module Hierarchy

## [Module A]
├── [Sub-component A1]
│   ├── [Function A1.1]
│   └── [Function A1.2]
└── [Sub-component A2]
    └── [Function A2.1]

## [Module B]
...
```

**dependency-graph.md contents:**
```markdown
# Dependency Graph

## Visual Representation

\`\`\`mermaid
graph TD
    A[Module A] -->|uses| B[Module B]
    A -->|reads| C[Module C]
    B -->|writes| D[Database]
\`\`\`

## Dependency Matrix

| Module | Depends On | Depended By |
|--------|------------|-------------|
| A | B, C | - |
| B | D | A |
```

---

### Phase 3: API Planning

**When to create:** After converting outlines to implementation-ready specifications.

**Files to create:**

| File | Purpose | When |
|------|---------|------|
| `api-contracts.md` | All endpoints with schemas | After API design |
| `call-signs.md` | Module-to-module communication | After verifying links |
| `coding-sequence.md` | Implementation order | Before Phase 3 checkpoint |

**api-contracts.md contents:**
```markdown
# API Contracts

## Endpoint: [METHOD /path]

### Request
\`\`\`json
{
  "field": "type"
}
\`\`\`

### Response
\`\`\`json
{
  "field": "type"
}
\`\`\`

### Errors
| Code | Meaning |
|------|---------|
| 400 | [reason] |
| 404 | [reason] |
```

**coding-sequence.md contents:**
```markdown
# Coding Sequence

## Critical Path

1. [Task 1] - [Module]
   - Blocks: [Task 2, Task 3]
   - Estimated: [time]

2. [Task 2] - [Module]
   - Depends on: [Task 1]
   - Blocks: [Task 4]

## Parallelizable Tasks

- [Task A] and [Task B] can run concurrently
```

---

### Phase 4: Agent Assignment

**When to create:** After determining LLM assignments for tasks.

**Files to create:**

| File | Purpose | When |
|------|---------|------|
| `work-division.md` | How work is split | After task grouping |
| `llm-capabilities.md` | Research findings on each LLM | After research |
| `assignment-matrix.md` | Final assignments | After user confirmation |
| `prompts/[agent].md` | Tailored prompts | For each assigned agent |

**assignment-matrix.md contents:**
```markdown
# Assignment Matrix

## Agent Assignments

| Agent Name | LLM | Tasks | Reason |
|------------|-----|-------|--------|
| backend-api | Claude | Auth, API | Best for complex logic (source: [URL]) |
| frontend-ui | GPT-4 | React UI | Strong at UI patterns (source: [URL]) |

## Handoff Points

| From Agent | To Agent | Trigger | Deliverable |
|------------|----------|---------|-------------|
| backend-api | frontend-ui | API complete | OpenAPI spec |
```

---

### Phase 5: Execution

**When to create:** During active development coordination.

**Files to create:**

| File | Purpose | When |
|------|---------|------|
| `handoff-protocol.md` | Standard handoff format | Before execution starts |
| `progress-tracker.md` | Status dashboard | Updated continuously |
| `shared-context.md` | Context all agents need | Before first task |
| `handoffs/handoff-NNN.md` | Individual handoffs | At each transition |

**progress-tracker.md contents:**
```markdown
# Progress Tracker

## Dashboard

| Agent | LLM | Status | Progress | Current Task | Blocker |
|-------|-----|--------|----------|--------------|---------|
| backend-api | Claude | Active | 60% | Auth module | None |
| frontend-ui | GPT-4 | Waiting | 0% | - | API Ready |

## Timeline

- [Date]: [milestone reached]
- [Date]: [milestone reached]

## Blockers

| ID | Description | Owner | Status |
|----|-------------|-------|--------|
| B1 | [blocker] | [agent] | Open |
```

---

### Phase 6: Integration

**When to create:** When merging work from all agents.

**Files to create:**

| File | Purpose | When |
|------|---------|------|
| `integration-checklist.md` | Verification steps | Before integration |
| `conflict-resolution.md` | Conflicts found and resolutions | During integration |
| `test-plan.md` | Integration test cases | Before testing |
| `final-report.md` | Complete project summary | After completion |

**final-report.md contents:**
```markdown
# Final Project Report

## Executive Summary
[1-2 paragraph overview]

## Architecture Overview
[Mermaid diagram]

## Modules Delivered
| Module | Owner | Status | Tests |
|--------|-------|--------|-------|
| [name] | [agent] | Complete | Passing |

## Decisions Log Summary
[Key decisions with sources]

## Known Issues
[Any remaining issues]

## Next Steps
[Recommendations for future work]
```

---

## Project State File (00-project-state.json)

This file tracks the current state and enables resumption:

```json
{
  "version": "1.0",
  "projectName": "My Project",
  "currentPhase": 3,
  "lastUpdated": "2025-01-15T10:30:00Z",
  "phases": {
    "1": {
      "status": "complete",
      "completedAt": "2025-01-14T15:00:00Z",
      "artifacts": [
        "01-discovery/outline-v1.md",
        "01-discovery/outline-detailed.md",
        "01-discovery/architecture-diagram.md",
        "01-discovery/decisions-log.md"
      ]
    },
    "2": {
      "status": "complete",
      "completedAt": "2025-01-15T09:00:00Z",
      "artifacts": [
        "02-structure/module-hierarchy.md",
        "02-structure/dependency-graph.md",
        "02-structure/risk-assessment.md",
        "02-structure/verbal-links.md"
      ]
    },
    "3": {
      "status": "in_progress",
      "startedAt": "2025-01-15T09:30:00Z",
      "artifacts": [
        "03-api-planning/api-contracts.md"
      ]
    },
    "4": { "status": "pending" },
    "5": { "status": "pending" },
    "6": { "status": "pending" }
  },
  "decisions": [
    {
      "id": "D001",
      "phase": 1,
      "decision": "Use PostgreSQL for database",
      "reason": "Relational data with complex queries",
      "source": "https://postgresql.org/docs",
      "date": "2025-01-14"
    }
  ],
  "agents": [],
  "blockers": [],
  "context": {
    "projectVision": "...",
    "targetUsers": "...",
    "coreModules": []
  }
}
```

---

## Updating Project State

After EVERY significant action, update `00-project-state.json`:

1. **Phase transition:**
   ```json
   {
     "phases": {
       "[N]": { "status": "complete", "completedAt": "[timestamp]" },
       "[N+1]": { "status": "in_progress", "startedAt": "[timestamp]" }
     }
   }
   ```

2. **New artifact created:**
   ```json
   {
     "phases": {
       "[N]": {
         "artifacts": ["...", "new-file.md"]
       }
     }
   }
   ```

3. **Decision made:**
   ```json
   {
     "decisions": [
       "...",
       {
         "id": "DXXX",
         "phase": N,
         "decision": "...",
         "reason": "...",
         "source": "...",
         "date": "..."
       }
     ]
   }
   ```

4. **Blocker identified:**
   ```json
   {
     "blockers": [
       {
         "id": "BXXX",
         "description": "...",
         "owner": "...",
         "status": "open",
         "identifiedAt": "..."
       }
     ]
   }
   ```

---

## Format Guidelines

### Markdown Files

1. **Headers:** Use ATX-style (`#`, `##`, `###`)
2. **Lists:** Use `-` for unordered, `1.` for ordered
3. **Code blocks:** Use triple backticks with language specifier
4. **Tables:** Use GitHub-flavored markdown tables
5. **Diagrams:** Use Mermaid syntax in fenced code blocks

### Naming Conventions

- Lowercase with hyphens: `api-contracts.md`
- Numbered prefixes for phases: `01-discovery/`
- Version suffixes when needed: `outline-v2.md`
- Agent names in prompts: `prompts/backend-api.md`

### Content Standards

1. **Every decision must have a source URL**
2. **Every API must have request/response examples**
3. **Every module must have clear purpose statement**
4. **Every risk must have mitigation strategy**
5. **Every diagram must have text explanation**

---

## Initialization Command

When starting a new project, create the directory structure:

```bash
mkdir -p .framework-blueprints/{01-discovery,02-structure,03-api-planning,04-agent-assignment/prompts,05-execution/handoffs,06-integration}
```

Then initialize `00-project-state.json` with:

```json
{
  "version": "1.0",
  "projectName": "",
  "currentPhase": 1,
  "lastUpdated": "",
  "phases": {
    "1": { "status": "in_progress", "startedAt": "", "artifacts": [] },
    "2": { "status": "pending", "artifacts": [] },
    "3": { "status": "pending", "artifacts": [] },
    "4": { "status": "pending", "artifacts": [] },
    "5": { "status": "pending", "artifacts": [] },
    "6": { "status": "pending", "artifacts": [] }
  },
  "decisions": [],
  "agents": [],
  "blockers": [],
  "context": {}
}
```

---

## Resume Protocol

When user asks to resume a project:

1. Read `00-project-state.json`
2. Identify `currentPhase`
3. List completed artifacts
4. Show pending items
5. Ask user where to continue

Example:
```
Project: My Project
Current Phase: 3 (API Planning)

Completed:
- Phase 1: Discovery (4 artifacts)
- Phase 2: Structure (4 artifacts)

In Progress:
- api-contracts.md (partial)

Where would you like to continue?
1. Continue API planning
2. Review previous phases
3. See full artifact list
```

---

## Integration with Framework Developer Workflow

This skill integrates with the 6-phase workflow:

| Phase | Skill Action |
|-------|--------------|
| 1. Discovery | Create outline and decisions files |
| 2. Structure | Create hierarchy and dependency files |
| 3. Planning | Create API contracts and sequence |
| 4. Agents | Create assignment matrix and prompts |
| 5. Execution | Maintain tracker and handoffs |
| 6. Integration | Create checklist and final report |

At each phase checkpoint, prompt:
```
Phase [N] complete. Save blueprints now?
[Y] Save all artifacts
[N] Continue without saving
[R] Review before saving
```

---

## Template Location

Templates for common files are available in:
`skills/blueprint-generation/templates/`

Use these as starting points and customize for each project.
