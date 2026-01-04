---
description: Force a context refresh by re-reading all blueprint files and recent source changes. Use this when the conversation feels "drifted" or after a long chat session to re-seed Claude's context with accurate state.
argument-hint: "[optional: 'full' for complete re-read, 'quick' for state only]"
allowed-tools: Read, Glob, Grep, Bash
---

# Checkpoint Command

This command performs a **manual context reset** - it forces me to re-read all critical files and generate a "State of the Union" summary.

## Why Use This?

After long chat sessions, my context window may have:
- Lost important details during compaction
- Drifted from the approved architecture
- Forgotten specific variable names, ports, or paths
- Accumulated outdated assumptions

Running `/checkpoint` clears the "fluff" and re-seeds my context with accurate, file-based truth.

---

## Executing Checkpoint

$ARGUMENTS

### Mode: Quick (default)
Re-reads only the state file and generates a summary.

### Mode: Full
Re-reads ALL blueprint files plus the last 3 modified source files.

---

## Checkpoint Process

### Step 1: Read State File
```
Reading: .framework-blueprints/00-project-state.json
```

### Step 2: Read All State Summaries
```
Reading: .framework-blueprints/*/state-summary-*.md
```

### Step 3: Read Core Blueprints (Full Mode)
```
Reading: .framework-blueprints/01-discovery/outline-v1.md
Reading: .framework-blueprints/03-api-planning/api-contracts.md
Reading: .framework-blueprints/05-execution/progress-tracker.md
```

### Step 4: Find Recently Modified Source Files (Full Mode)
```bash
find src/ -type f -name "*.ts" -o -name "*.tsx" -o -name "*.js" | xargs ls -t | head -3
```

### Step 5: Verify Link Integrity
```bash
grep -r "TODO\|FIXME\|BROKEN" .framework-blueprints/
```

### Step 6: Generate State of the Union

---

## State of the Union Report

After reading all files, I will generate:

```markdown
# 🏛️ State of the Union

**Project:** [name]
**Generated:** [timestamp]
**Context Status:** ✅ Fresh

## Current Position
- **Phase:** [N] - [Name]
- **Phase Progress:** [X]%
- **Active Task:** [ID] - [Name]

## Critical Details (From State Summaries)
- Database: [type] on port [port]
- Auth: [method] using [library]
- Non-standard paths:
  - [path]: [reason]

## API Contracts Summary
| Endpoint | Method | Status |
|----------|--------|--------|
| /api/users | GET | ✅ Implemented |
| /api/users | POST | 🔄 In Progress |
| /api/auth/login | POST | ⏳ Pending |

## Recent Decisions
1. [Decision] - [Date] - [Source]
2. [Decision] - [Date] - [Source]

## Open Risks
- [Risk]: [Mitigation]

## Blockers
- [Blocker]: Waiting on [dependency]

## Next Actions
1. Complete: [current task]
2. Then: [next task]
3. Gate: [approval needed]

## Integrity Check
- ✅ All blueprint files present
- ✅ API contracts consistent
- ✅ No broken links found
- ⚠️ [any warnings]
```

---

## When to Use /checkpoint

| Situation | Recommendation |
|-----------|----------------|
| After 20+ messages | `/checkpoint` |
| Before starting new phase | `/checkpoint full` |
| After an error occurred | `/checkpoint full` |
| Feeling "off track" | `/checkpoint` |
| Before important decision | `/checkpoint` |
| Resuming after break | `/checkpoint full` |

---

## What This Fixes

| Problem | How Checkpoint Helps |
|---------|---------------------|
| Wrong endpoints | Re-reads api-contracts.md |
| Lost variable names | Re-reads state summaries |
| Forgotten decisions | Re-reads decisions-log.md |
| Duplicated work | Re-reads progress-tracker.md |
| Broken file paths | Runs link verification |

---

## Example Usage

```
User: /checkpoint

Claude: 🔄 Running context checkpoint...

Reading .framework-blueprints/00-project-state.json...
Reading state-summary-phase-5.md...
Verifying links...

# 🏛️ State of the Union

**Project:** TaskMaster Pro
**Phase:** 5 - Execution (65% complete)

**Critical Details Restored:**
- JWT_SECRET_KEY env var required
- Auth middleware at src/core/auth/ (not src/auth/)
- Database port: 5432

**Current Task:** T-012 - Implement refresh token rotation
**Next Task:** T-013 - Add OAuth providers (blocked by T-012)

✅ Context refreshed. I now have accurate state from files.
```

---

## Automatic Checkpoints

Checkpoints also run automatically:
- At phase transitions (50% and 100%)
- After errors (via ErrorRecovery hook)
- On session start (if blueprints exist)

You can always run `/checkpoint` manually if needed.
