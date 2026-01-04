---
description: Rollback to a previous checkpoint state. Use when work needs to be reverted or to recover from errors.
argument-hint: "[list|phase-N|checkpoint-ID|latest]"
allowed-tools: Read, Write, Bash, Glob
---

# Rollback Command

This command allows you to revert the framework blueprints and state to a previous checkpoint.

## Usage

```
/rollback list              # Show available rollback points
/rollback phase-3           # Rollback to start of Phase 3
/rollback checkpoint-5      # Rollback to specific checkpoint ID
/rollback latest            # Rollback to most recent checkpoint
```

## Mode: $ARGUMENTS

---

## Rollback: List

Show all available rollback points:

### Step 1: Find Git Tags

```bash
git tag -l "checkpoint-*" "handoff-*" "phase-*" --sort=-creatordate
```

### Step 2: Find Backup Files

```bash
ls -la .framework-blueprints/backups/
```

### Step 3: Display Available Points

```markdown
## Available Rollback Points

### Git Checkpoints
| Tag | Date | Description |
|-----|------|-------------|
| phase-3-complete | 2025-01-03 14:00 | Phase 3 completed |
| handoff-HO-001 | 2025-01-03 12:30 | Backend → Frontend |
| checkpoint-5 | 2025-01-03 11:00 | 50% Phase 5 |

### File Backups
| File | Date | Size |
|------|------|------|
| state-2025-01-03T14-00-00Z.json | 14:00 | 4.2KB |
| state-2025-01-03T12-00-00Z.json | 12:00 | 3.8KB |

### Current State
- Phase: 5 (Execution)
- Progress: 65%
- Last update: 2025-01-03 15:30

**Usage:** `/rollback [tag-name]` or `/rollback checkpoint-5`
```

---

## Rollback: Phase-N

Rollback to the start of a specific phase:

### Step 1: Find Phase Checkpoint

```bash
git log --oneline --grep="Phase $N" | head -1
```

### Step 2: Confirm with User

```markdown
⚠️ **Rollback Confirmation**

You are about to rollback to: **Phase [N] Start**

This will:
- Revert all files in `.framework-blueprints/`
- Restore state to Phase [N] beginning
- Discard work done after this point

**Work that will be lost:**
- [List of phases/tasks completed after this point]

**Proceed?** [yes/no]
```

### Step 3: Execute Rollback

```bash
# Stash any uncommitted changes
git stash push -m "pre-rollback-backup"

# Checkout the phase checkpoint
git checkout phase-$N-start -- .framework-blueprints/

# Or if using file backups:
cp .framework-blueprints/backups/state-at-phase-$N.json \
   .framework-blueprints/00-project-state.json
```

### Step 4: Update State

```json
{
  "currentPhase": N,
  "phases": {
    "N": { "status": "in_progress", "progress": 0 }
    // Later phases reset to pending
  }
}
```

### Step 5: Confirm Success

```markdown
✅ **Rollback Complete**

- Reverted to: Phase [N] Start
- State file restored
- Blueprint files restored

**Current Status:**
- Phase: [N] - [Name]
- Progress: 0%

**Next Steps:**
1. Review the restored state
2. Continue with Phase [N] tasks

**Recovery Info:**
- Pre-rollback backup: `git stash list` → stash@{0}
- To undo rollback: `git stash pop`
```

---

## Rollback: Checkpoint-ID

Rollback to a specific checkpoint:

### Step 1: Find Checkpoint

```bash
git show checkpoint-$ID --stat
```

### Step 2: Verify Checkpoint Exists

If not found:
```markdown
❌ Checkpoint `checkpoint-$ID` not found.

**Available checkpoints:**
[List available checkpoints]

**Did you mean:** `checkpoint-[closest match]`?
```

### Step 3: Show What Will Change

```bash
git diff checkpoint-$ID -- .framework-blueprints/
```

### Step 4: Execute and Confirm

Same as Phase rollback.

---

## Rollback: Latest

Rollback to the most recent checkpoint:

### Step 1: Find Latest

```bash
git tag -l "checkpoint-*" --sort=-creatordate | head -1
```

### Step 2: Execute

Rollback to that checkpoint.

---

## Safety Features

### Pre-Rollback Backup

Always create a backup before rollback:

```bash
# Create timestamped backup
cp .framework-blueprints/00-project-state.json \
   .framework-blueprints/backups/pre-rollback-$(date +%Y%m%dT%H%M%S).json

# Git stash for full backup
git stash push -m "pre-rollback-$(date +%Y%m%dT%H%M%S)"
```

### Rollback Recovery

If rollback was a mistake:

```bash
# From git stash
git stash pop

# From file backup
cp .framework-blueprints/backups/pre-rollback-*.json \
   .framework-blueprints/00-project-state.json
```

---

## Creating Checkpoints

Checkpoints are created automatically at:
- Phase transitions (50% and 100%)
- Handoff completions
- Manual `/checkpoint` command

To create a manual checkpoint:

```bash
git add .framework-blueprints/
git commit -m "Checkpoint: [description]"
git tag checkpoint-$(date +%s) -m "[description]"
```

---

## Error Handling

### Dirty Working Directory

```markdown
⚠️ **Cannot rollback - uncommitted changes detected**

You have uncommitted changes that would be lost.

**Options:**
1. Commit changes first: `git add . && git commit -m "WIP"`
2. Stash changes: `git stash push -m "before-rollback"`
3. Force rollback (DESTRUCTIVE): `/rollback --force [target]`

What would you like to do?
```

### Missing Checkpoint

```markdown
❌ **Checkpoint not found:** `checkpoint-99`

The specified checkpoint does not exist.

**Available checkpoints:**
[List]

**Suggestions:**
1. Check the checkpoint ID
2. Use `/rollback list` to see all available points
```

---

## Audit Trail

All rollbacks are logged:

```json
// In 00-project-state.json
{
  "rollbacks": [
    {
      "id": "RB-001",
      "from": "phase-5-65%",
      "to": "checkpoint-5",
      "reason": "Integration errors",
      "timestamp": "2025-01-03T16:00:00Z",
      "recoveryPath": "git stash@{0}"
    }
  ]
}
```
