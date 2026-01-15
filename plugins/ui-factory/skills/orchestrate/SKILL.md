---
name: orchestrate
description: Fast parallel execution. One file = One owner. Maximum speed. Supports multi-terminal mode for 10+ files with Claude/Gemini/Qwen.
argument-hint: "[feature-name] [--multi] [--terminals N]"
---

# /orchestrate

Arguments: `$ARGUMENTS`

## Mode Detection

| Argument | Mode | Description |
|----------|------|-------------|
| (none) | STANDARD | Single context, 4-6 agents |
| `--multi` | MULTI-TERMINAL | 6 terminals × models, 24+ workers |
| `--terminals N` | MULTI-TERMINAL | Specify terminal count (default: 6) |

**Auto-detect MULTI mode when:**
- 10+ files need modification
- Task explicitly mentions "heavy" or "parallel"
- User says "use all terminals" or similar

---

## FIRST: Check for Specs

**If `specs/[feature]/design.md` exists → USE IT as source of truth.**

1. Read `specs/[feature]/design.md` for requirements
2. Read `specs/[feature]/implementation.md` for progress
3. Use file assignments from design if specified
4. After work → update `implementation.md`

**If no specs exist:**
- For small tasks: proceed without
- For features: suggest `/spec [name]` first

---

## STANDARD MODE (Single Context)

### What I'll Do
1. **Analyze the task** - Identify files that need editing
2. **Assign ownership** - Each file/directory gets ONE agent
3. **Spawn parallel** - All agents in ONE message, background mode
4. **Verify once** - Type check, lint, test after all complete

### The ONE Rule
**ONE FILE = ONE OWNER**
- Before spawning agents, assign file ownership
- No two agents edit the same file
- If overlap unavoidable, use single agent instead

### Execution Pattern
```json
[
  {
    "description": "[workstream name]",
    "subagent_type": "[appropriate type]",
    "run_in_background": true,
    "prompt": "You OWN: [files]. Task: [work]. NO other agent touches these."
  }
]
```

### When to Use Standard Mode
- Less than 10 files
- Tightly coupled files that must change together
- Simple parallel workstreams

---

## MULTI-TERMINAL MODE (Multiple Contexts)

### When to Use
- 10+ files need modification
- Heavy parallelization needed
- Want to use different models (Gemini, Qwen) for different tasks
- Context would bloat in single session

### Architecture
```
┌─────────────────────────────────────────────────────────────────┐
│                    /orchestrate --multi                         │
│                                                                 │
│  1. Read specs/[feature]/design.md                              │
│  2. Split work into N packages (one per terminal)               │
│  3. Assign optimal model per package                            │
│  4. Spawn N terminals                                           │
│  5. Each terminal works independently                           │
│  6. Results merge via specs/implementation.md                   │
└─────────────────────────────────────────────────────────────────┘

Terminal 1 (Claude)    Terminal 2 (Gemini)   Terminal 3 (Qwen)
┌─────────────┐       ┌─────────────┐       ┌─────────────┐
│ Complex     │       │ Research    │       │ Boilerplate │
│ Logic       │       │ Long context│       │ Simple CRUD │
│ Architecture│       │ Analysis    │       │ Repetitive  │
└─────────────┘       └─────────────┘       └─────────────┘
```

### Model Assignment Strategy

| Model | Best For | Assign When |
|-------|----------|-------------|
| **Claude (opus)** | Complex logic, architecture, refactoring | Critical paths, core business logic |
| **Claude (sonnet)** | General coding, balanced tasks | Most implementation work |
| **Claude (haiku)** | Simple edits, boilerplate | Config files, simple CRUD |
| **Gemini** | Research, long context, analysis | Documentation, large file analysis |
| **Qwen** | Boilerplate, repetitive tasks | Tests, simple components, migrations |

### Work Package Format

Each terminal receives `.claude/packages/[feature]-pkg[N].md`:

```markdown
# Work Package: [Package Name]
## Model: [claude-sonnet | gemini | qwen]

## Your Scope
Files you OWN (only you modify):
- file1.ts
- file2.ts

Files you can READ (don't modify):
- specs/[feature]/design.md
- shared/types.ts

## Your Tasks
1. [Specific task]
2. [Specific task]

## Constraints
- Follow patterns in design.md
- When done, append to specs/[feature]/implementation.md:
  ## [DONE: [Package Name]]
  - Completed: [what you did]
  - Files: [files modified]
- Do NOT modify files outside ownership

## Context
[Excerpt from design.md relevant to this package]
```

### Terminal Spawning

**Windows (Windows Terminal):**
```powershell
# Claude terminal
wt -w 0 nt --title "Pkg-1-Claude" cmd /c "claude --prompt .claude/packages/pkg1.md"

# Gemini terminal (if installed)
wt -w 0 nt --title "Pkg-2-Gemini" cmd /c "gemini --prompt .claude/packages/pkg2.md"

# Qwen terminal (if installed)
wt -w 0 nt --title "Pkg-3-Qwen" cmd /c "qwen --prompt .claude/packages/pkg3.md"
```

**Linux/Mac:**
```bash
# Claude
gnome-terminal --tab --title="Pkg-1" -- claude --prompt .claude/packages/pkg1.md

# Gemini
gnome-terminal --tab --title="Pkg-2" -- gemini --prompt .claude/packages/pkg2.md
```

### Coordination Protocol

No direct inter-agent communication. Coordinate via filesystem:

| File | Purpose | Access |
|------|---------|--------|
| `specs/[feature]/design.md` | Requirements | Read-only |
| `specs/[feature]/implementation.md` | Progress | Append-only |
| `.claude/packages/*.md` | Work packages | Read-only |
| `.claude/locks/*.lock` | File locks | Create/delete |

### Multi-Terminal Execution Steps

1. **Create packages directory:**
   ```bash
   mkdir -p .claude/packages .claude/locks
   ```

2. **Generate work packages:**
   - Analyze task scope
   - Group files by natural boundaries
   - Assign model based on task complexity
   - Write package files

3. **Create lock files:**
   ```bash
   touch .claude/locks/[filename].lock
   ```

4. **Spawn terminals:**
   - One terminal per package
   - Each with assigned model
   - All start simultaneously

5. **Monitor progress:**
   - Watch `specs/[feature]/implementation.md` for updates
   - Check `.claude/locks/` for completion

6. **Merge results:**
   - Collect all [DONE] and [BLOCKED] sections
   - Run final verification (type check, lint, test)
   - Update implementation.md with summary

---

## Model Selection Decision Tree

```
Task received
├── Complex architecture/refactoring?
│   └── Claude Opus
├── Core business logic?
│   └── Claude Sonnet
├── Research/analysis/long docs?
│   └── Gemini
├── Simple CRUD/boilerplate?
│   └── Qwen or Claude Haiku
├── Tests/migrations?
│   └── Qwen
└── General implementation?
    └── Claude Sonnet
```

---

## Output Format

### Standard Mode
```
╔═══════════════════════════════════════════════════════╗
║              ORCHESTRATE - STANDARD                   ║
╠═══════════════════════════════════════════════════════╣
║ Task: [description]                                   ║
║ Files: [count]                                        ║
║ Agents: [count]                                       ║
╠═══════════════════════════════════════════════════════╣
║ Agent 1: [name]     Files: [list]     [✓]            ║
║ Agent 2: [name]     Files: [list]     [✓]            ║
╠═══════════════════════════════════════════════════════╣
║ Status: COMPLETE                                      ║
╚═══════════════════════════════════════════════════════╝
```

### Multi-Terminal Mode
```
╔═══════════════════════════════════════════════════════╗
║           ORCHESTRATE - MULTI-TERMINAL                ║
╠═══════════════════════════════════════════════════════╣
║ Feature: [name]                                       ║
║ Terminals: [N]                                        ║
║ Models: Claude(3), Gemini(2), Qwen(1)                ║
╠═══════════════════════════════════════════════════════╣
║ T1: core-logic       Claude    [✓] 12 files         ║
║ T2: api-layer        Claude    [✓] 8 files          ║
║ T3: research         Gemini    [✓] analysis done    ║
║ T4: components       Claude    [✓] 6 files          ║
║ T5: tests            Qwen      [✓] 15 files         ║
║ T6: migrations       Qwen      [✓] 4 files          ║
╠═══════════════════════════════════════════════════════╣
║ Status: COMPLETE                                      ║
║ Total Files: 45                                       ║
║ Time: 12m 34s                                         ║
╚═══════════════════════════════════════════════════════╝
```

---

## Error Recovery

See `references/recovery.md` for:
- Retry patterns
- Model escalation (Haiku → Sonnet → Opus)
- Contract change handling
- Multi-terminal failure recovery

---

## Bundled References

- `references/recovery.md` - Error handling and retry patterns
- `references/model-strengths.md` - Detailed model comparison for task assignment
