---
description: Use this skill to track and persist project state across sessions. Provides state file schema, checkpointing protocols, and context recovery procedures for the Framework Developer Orchestrator.
---

# Project State Management Skill

This skill teaches the Framework Developer Orchestrator how to track and persist project state across sessions, ensuring continuity and enabling seamless resumption of work.

## Overview

The state management system maintains a single source of truth for:
- Current workflow phase (1-6)
- All decisions made (with sources)
- Module definitions and dependencies
- Agent assignments and status
- Handoff tracking between agents
- Risk registry
- **Checkpoint summaries (critical for context recovery)**

**State File Location:** `.framework-blueprints/00-project-state.json`

---

## CRITICAL: Context Window Mitigation

Claude Code has a limited context window that gets compacted during long sessions. This causes:
- Loss of specific details (variable names, ports, paths)
- "Guessing" instead of reading from source
- Drift from approved architecture

### The Solution: File-Based Memory

**Chat = Volatile Memory** (gets compacted, loses details)
**Files = Persistent Memory** (always accurate if maintained)

This skill enforces:
1. **Immediate writes** - Never defer state updates
2. **Just-in-time reads** - Always read before writing
3. **Checkpoint summaries** - Capture details before they're lost
4. **Read-Modify-Write** - Never overwrite with summaries

---

## 1. State File Initialization

### When to Initialize

Initialize a new state file when:
1. User starts a new project with `/framework-dev`
2. No existing state file is found in the project directory
3. User explicitly requests a fresh start

### Initial State Template

```json
{
  "projectName": "",
  "version": "1.0.0",
  "createdAt": "",
  "updatedAt": "",
  "currentPhase": 1,
  "phases": {
    "1": { "status": "in_progress", "startedAt": "", "progress": 0 },
    "2": { "status": "pending", "progress": 0 },
    "3": { "status": "pending", "progress": 0 },
    "4": { "status": "pending", "progress": 0 },
    "5": { "status": "pending", "progress": 0 },
    "6": { "status": "pending", "progress": 0 }
  },
  "decisions": [],
  "modules": [],
  "agents": [],
  "handoffs": [],
  "risks": [],
  "checkpoints": [],
  "criticalDetails": {
    "ports": {},
    "envVars": [],
    "nonStandardPaths": {},
    "apiQuirks": []
  }
}
```

---

## 2. MANDATORY: Checkpointing Protocol

### Why Checkpoints Matter

During long sessions, Claude's context window compacts. Details like:
- `JWT_SECRET_KEY` environment variable name
- Port 5432 for PostgreSQL
- Non-standard path `src/core/auth/` instead of `src/auth/`

...get lost because they seem "minor" but are critical for correct code.

### Checkpoint Triggers

Create checkpoints at:

| Trigger | Action |
|---------|--------|
| Phase 50% complete | Generate `state-summary-phase-N.md` |
| Phase 100% complete | Generate full checkpoint + update state |
| Before long task | Capture current critical details |
| After error recovery | Re-capture state from files |
| User runs `/checkpoint` | Full context refresh |

### State Summary File Format

Create `state-summary-phase-N.md` with this structure:

```markdown
# Phase [N] State Summary

**Generated:** [ISO timestamp]
**Phase:** [Name]
**Progress:** [X]%

## Critical Details (PRESERVE THESE)

### Environment Variables
- `DATABASE_URL`: Connection string for PostgreSQL
- `JWT_SECRET_KEY`: Used in src/core/auth/jwt.ts
- `REDIS_URL`: Cache connection (optional)

### Ports & Connections
- PostgreSQL: 5432
- Redis: 6379
- API Server: 3000
- Frontend Dev: 5173

### Non-Standard Paths
| Expected | Actual | Reason |
|----------|--------|--------|
| src/auth/ | src/core/auth/ | Grouped with core utilities |
| src/db/ | src/infrastructure/database/ | Hexagonal architecture |

### API Quirks
- POST /api/users returns 201, not 200
- All dates are ISO 8601 UTC, not local
- Pagination uses cursor, not offset
- Auth header format: `Bearer <token>` (space required)

### Variable Names (Exact)
- User ID field: `userId` (not `user_id` or `id`)
- Timestamp fields: `createdAt`, `updatedAt` (camelCase)
- JWT payload: `{ sub: userId, iat: timestamp, exp: timestamp }`

## Decisions Made This Phase
1. [D001] Chose PostgreSQL over MongoDB - [source URL]
2. [D002] Using Hexagonal Architecture - [source URL]

## Tasks Completed
- [x] T001: Database schema design
- [x] T002: Auth module structure
- [ ] T003: JWT implementation (in progress)

## Open Questions
- Should refresh tokens be stored in Redis or DB?
- Rate limiting strategy TBD

## Blockers
- None currently
```

### Atomic Updates

**NEVER do this:**
```json
// BAD: Overwrites detailed state with summary
{
  "decisions": ["Used PostgreSQL"]
}
```

**ALWAYS do this:**
```json
// GOOD: Preserves existing details, adds new
{
  "decisions": [
    ...existingDecisions,
    {
      "id": "D003",
      "topic": "Cache Strategy",
      "choice": "Redis",
      "source": "https://redis.io/docs/",
      "reasoning": "Need sub-ms latency for session lookup",
      "date": "2025-01-03T10:30:00Z"
    }
  ]
}
```

### Read-Modify-Write Pattern

```typescript
// 1. READ current state
const currentState = await readFile('.framework-blueprints/00-project-state.json');
const state = JSON.parse(currentState);

// 2. MODIFY only what changed
state.decisions.push(newDecision);
state.updatedAt = new Date().toISOString();
state.phases["3"].progress = 75;

// 3. WRITE complete state back
await writeFile('.framework-blueprints/00-project-state.json', JSON.stringify(state, null, 2));
```

---

## 3. State Update Operations

### Update Triggers

Update the state file **IMMEDIATELY** after every significant action:

| Event | State Fields to Update | Priority |
|-------|------------------------|----------|
| Decision made | `decisions[]`, `updatedAt` | IMMEDIATE |
| Module defined | `modules[]`, `updatedAt` | IMMEDIATE |
| Agent assigned | `agents[]`, `updatedAt` | IMMEDIATE |
| Task completed | `phases[n].progress`, `updatedAt` | IMMEDIATE |
| Risk identified | `risks[]`, `updatedAt` | IMMEDIATE |
| Phase transition | `currentPhase`, `phases[n].status` | IMMEDIATE |
| Critical detail found | `criticalDetails`, `updatedAt` | IMMEDIATE |

### Recording Critical Details

When you encounter specifics, record them immediately:

```json
{
  "criticalDetails": {
    "ports": {
      "postgresql": 5432,
      "redis": 6379,
      "api": 3000
    },
    "envVars": [
      { "name": "DATABASE_URL", "description": "PostgreSQL connection", "required": true },
      { "name": "JWT_SECRET_KEY", "description": "JWT signing key", "required": true },
      { "name": "REDIS_URL", "description": "Redis connection", "required": false }
    ],
    "nonStandardPaths": {
      "auth": { "expected": "src/auth/", "actual": "src/core/auth/", "reason": "Core grouping" },
      "config": { "expected": "src/config/", "actual": "src/bootstrap/config.ts", "reason": "Single file" }
    },
    "apiQuirks": [
      "POST /users returns 201 not 200",
      "All timestamps are UTC ISO 8601",
      "Cursor pagination, not offset"
    ]
  }
}
```

---

## 4. Resuming from Saved State

### Resume Process

When the user returns to continue work:

1. **Check for state file:**
   ```bash
   ls .framework-blueprints/00-project-state.json
   ```

2. **Read state file:**
   ```bash
   cat .framework-blueprints/00-project-state.json
   ```

3. **Find and read all checkpoint summaries:**
   ```bash
   ls .framework-blueprints/*/state-summary-*.md
   cat .framework-blueprints/05-execution/state-summary-phase-5.md
   ```

4. **Display resume summary to user**

5. **Confirm with user before proceeding**

### Resume Summary Display

```markdown
## Project Resume: [projectName]

**Last Updated:** [date]
**Current Phase:** [N] - [Name] ([X]% complete)

### Restored Critical Details
- Database: PostgreSQL on port 5432
- Auth: JWT with refresh tokens
- Non-standard paths detected and loaded

### Phase Progress
- [x] Phase 1: Discovery (100%)
- [x] Phase 2: Structure (100%)
- [x] Phase 3: API Planning (100%)
- [~] Phase 4: Agent Assignment (50%)
- [ ] Phase 5: Execution (0%)
- [ ] Phase 6: Integration (0%)

### Active Task
T-008: Assign frontend tasks to agents

### Open Blockers
None

**Resume from Phase 4?** (yes/no/review)
```

---

## 5. State Validation Rules

### Validation on Load

Before using state, validate:

```typescript
function validateState(state: unknown): ValidationResult {
  const errors: string[] = [];
  const warnings: string[] = [];

  // Required fields
  const requiredFields = [
    'projectName', 'version', 'createdAt', 'updatedAt',
    'currentPhase', 'phases', 'decisions', 'modules', 
    'agents', 'handoffs', 'risks'
  ];

  for (const field of requiredFields) {
    if (!(field in state)) {
      errors.push(`Missing required field: ${field}`);
    }
  }

  // Phase consistency
  const currentPhase = state.currentPhase;
  for (let i = 1; i < currentPhase; i++) {
    if (state.phases[String(i)]?.status !== 'completed') {
      warnings.push(`Phase ${i} should be completed (current phase is ${currentPhase})`);
    }
  }

  // Decision sources
  state.decisions?.forEach((d, i) => {
    if (!d.source) {
      warnings.push(`Decision ${i} missing source URL`);
    }
  });

  return { valid: errors.length === 0, errors, warnings };
}
```

### Self-Healing

If validation finds issues:

1. **Missing fields:** Add with defaults, log warning
2. **Inconsistent phases:** Ask user to confirm correct phase
3. **Missing sources:** Flag decisions that need sources added

---

## 6. Backup and Version History

### Automatic Backups

Create backups at:
- Every phase transition
- Before any destructive operation
- On manual request (`/checkpoint`)
- Every 10 state updates

### Backup Directory Structure

```
.framework-blueprints/
├── 00-project-state.json           # Current state
├── backups/
│   ├── state-2025-01-03T10-00-00Z-phase-transition.json
│   ├── state-2025-01-03T11-30-00Z-checkpoint.json
│   └── state-2025-01-03T12-00-00Z-manual.json
└── history/
    └── decisions.log               # Append-only decision log
```

### Restore from Backup

```bash
# List available backups
ls -la .framework-blueprints/backups/

# Restore specific backup
cp .framework-blueprints/backups/state-2025-01-03T10-00-00Z.json \
   .framework-blueprints/00-project-state.json
```

---

## 7. Integration with Context Hygiene

### Pre-Read Protocol

Before ANY write operation:

```markdown
1. Read `.framework-blueprints/00-project-state.json`
   - Know current phase and progress
   - Load critical details (ports, paths, vars)

2. Read `.framework-blueprints/03-api-planning/api-contracts.md` (if exists)
   - Know correct endpoints
   - Know request/response schemas

3. Read latest `state-summary-phase-*.md` (if exists)
   - Restore any "minor" details that matter
```

### Post-Write Protocol

After ANY significant action:

```markdown
1. Update `00-project-state.json` immediately
   - Use Read-Modify-Write pattern
   - Never overwrite with summary

2. If at 50% or 100% of phase:
   - Generate `state-summary-phase-N.md`
   - Include ALL critical details

3. Announce completion:
   - "Task [X] finished and committed to blueprints"
   - Signal that task details can be cleared from chat
```

### The "Small Circle" Rule

During Phase 5 (Execution):

1. **One task at a time** - Never start Task B until Task A is verified
2. **PR-sized chunks** - Break large features into small, testable pieces
3. **Update tracker** - After each chunk, update `progress-tracker.md`
4. **Verify before proceed** - Tests pass OR user confirms before next task

---

## 8. Quick Reference

### State Update Checklist

- [ ] Read current state first (don't overwrite blindly)
- [ ] Add to arrays, don't replace them
- [ ] Include source URLs for decisions
- [ ] Update `updatedAt` timestamp
- [ ] Record critical details (ports, paths, vars)
- [ ] Create checkpoint at 50% phase progress

### Checkpoint Checklist

- [ ] Project name and current phase
- [ ] All critical environment variables
- [ ] All non-standard paths with reasons
- [ ] All API quirks and gotchas
- [ ] Exact variable names used
- [ ] Open blockers and questions

### Resume Checklist

- [ ] Read `00-project-state.json`
- [ ] Read all `state-summary-*.md` files
- [ ] Read `api-contracts.md` if Phase 3+
- [ ] Display summary to user
- [ ] Get explicit confirmation before proceeding

---

## 9. Error Recovery

When an error occurs:

1. **Re-read state file** - Context may have drifted
2. **Re-read checkpoints** - Restore critical details
3. **Verify against contracts** - Error might be wrong endpoint
4. **Check file paths** - Use Glob to find correct path
5. **Report to user** - Explain what went wrong and recovery steps

### Common Errors and Fixes

| Error | Likely Cause | Fix |
|-------|--------------|-----|
| 404 on API call | Wrong endpoint | Re-read api-contracts.md |
| Import not found | Wrong path | Grep for correct path |
| Type mismatch | Schema changed | Re-read api-contracts.md |
| Test failure | Outdated assumption | Re-read state-summary |
| Missing env var | Forgot to document | Add to criticalDetails |

---

## 10. Best Practices

### Do's

1. **Update state immediately** - Never "remember" for later
2. **Cite sources** - Every decision needs a URL
3. **Create checkpoints** - Capture details before they're lost
4. **Use Read-Modify-Write** - Preserve existing details
5. **Verify after changes** - Grep for broken links
6. **Keep checkpoints small** - Focus on critical details

### Don'ts

1. **Don't rely on chat memory** - It gets compacted
2. **Don't summarize over details** - Keep specifics
3. **Don't skip validation** - Always verify state on load
4. **Don't assume paths** - Always verify with Glob
5. **Don't guess endpoints** - Always read contracts
6. **Don't proceed without state sync** - Read first, always

---

## Related Skills

- `handoff-protocol` - Details on agent handoff procedures
- `llm-capability-matching` - How to assign agents based on capabilities
- `architecture-decision-records` - ADR format for decisions
- `api-design-principles` - API contract design

## References

- State schema: `references/state-schema.json`
