---
description: Auto-review pipeline — runs the full Framework Developer Orchestrator with auto-decisions. Rough idea in, fully reviewed plan out. No intermediate questions except premises and user challenges.
argument-hint: "[project description]"
allowed-tools: WebSearch, WebFetch, Read, Write, Glob, Grep, Bash, AskUserQuestion, Task, TodoWrite, Skill, Agent
---

# /autoplan — Auto-Review Pipeline

One command. Rough idea in, fully reviewed plan out.

Runs the full Framework Developer Orchestrator (all 6 phases) with auto-decisions using the 6 decision principles below. Taste decisions (where reasonable people could disagree) are surfaced at a final approval gate.

## The 6 Decision Principles

These rules auto-answer every intermediate question:

1. **Choose completeness** — Ship the whole thing. Pick the approach that covers more edge cases.
2. **Boil lakes** — Fix everything in the blast radius (files modified + direct importers). Auto-approve expansions that are in blast radius AND < 1 day effort (< 5 files, no new infra).
3. **Pragmatic** — If two options fix the same thing, pick the cleaner one. 5 seconds choosing, not 5 minutes.
4. **DRY** — Duplicates existing functionality? Reject. Reuse what exists.
5. **Explicit over clever** — 10-line obvious fix > 200-line abstraction. Pick what a new contributor reads in 30 seconds.
6. **Bias toward action** — Merge > review cycles > stale deliberation. Flag concerns but don't block.

**Conflict resolution (context-dependent tiebreakers):**
- **Discovery phase:** P1 (completeness) + P2 (boil lakes) dominate.
- **Planning phase:** P5 (explicit) + P3 (pragmatic) dominate.
- **Execution phase:** P5 (explicit) + P1 (completeness) dominate.

## Decision Classification

**Mechanical** — one clearly right answer. Auto-decide silently.
Examples: research before recommending (always yes), run outside voice (always yes).

**Taste** — reasonable people could disagree. Auto-decide with recommendation, but surface at final gate. Three sources:
1. Close approaches — top two both viable with different tradeoffs
2. Borderline scope — ambiguous whether to include or defer
3. Outside voice disagreements — subagent recommends differently with valid reasoning

**User Challenge** — both the orchestrator and outside voice agree the user's stated direction should change. NEVER auto-decided. Goes to final gate with:
- What the user said (their original direction)
- What both recommend (the change)
- Why (the reasoning)
- What context we might be missing
- If we're wrong, the cost is: [consequence]

## Sequential Execution — MANDATORY

Phases MUST execute in strict order: 1 → 2 → 3 → 3.5 → 4 → 5 → 6.
Each phase MUST complete fully before the next begins.
NEVER run phases in parallel.

## What "Auto-Decide" Means

Auto-decide replaces the USER's judgment with the 6 principles. It does NOT replace the ANALYSIS. Every phase executes at the same depth as interactive mode. The only change is who answers.

**Two exceptions — never auto-decided:**
1. **Premises** (Phase 1, Step 1.0) — require human judgment about what problem to solve
2. **User Challenges** — when both orchestrator and outside voice agree the user's stated direction should change

**You MUST still:**
- READ the actual code, diffs, and files each step references
- PRODUCE every output each step requires (diagrams, tables, alternatives)
- IDENTIFY every issue each step is designed to catch
- DECIDE each issue using the 6 principles
- LOG each decision in the audit trail
- WRITE all required artifacts to disk

**You MUST NOT:**
- Compress a phase into a one-liner summary
- Write "no issues found" without showing what you examined
- Skip a step because "it doesn't apply" without stating what you checked
- Produce a summary instead of the required output

## Phase Execution

### Phase 0: Intake

1. Run system audit (git log, diff, read CLAUDE.md, TODOS.md)
2. If `resume` argument: run same resume flow as `/framework-dev resume`, then re-enter auto-decision pipeline. Read `autoplan-decisions.md` if it exists to restore decision context and scope mode.
3. Detect UI scope (grep plan for view/rendering terms)
4. Auto-select scope mode: **SELECTIVE EXPANSION** (default for autoplan)

### Phases 1-6: Full Depth

Follow the Framework Developer agent definition at full depth:
- Phase 1: Discovery (GATE: present premises to user — NOT auto-decided)
- Phase 2: Structure (auto-decide using principles)
- Phase 3: Planning (auto-decide using principles)
- Phase 3.5: Outside Voice (always run — P6 bias toward action)
- Phase 4: Agent Assignment (auto-decide using principles)
- Phase 5: Execution (auto-decide using principles)
- Phase 6: Integration (auto-decide using principles)

### Decision Audit Trail

Log every auto-decision:
```
## Autoplan Decision Log

| # | Phase | Issue | Decision | Principle | Type |
|---|-------|-------|----------|-----------|------|
| 1 | 1 | DB choice | PostgreSQL | P1 completeness | Mechanical |
| 2 | 2 | Cache layer | Add Redis | P2 boil lakes | Taste |
| 3 | 3 | GraphQL vs REST | REST | P5 explicit | Mechanical |
```

Write to `.framework-blueprints/autoplan-decisions.md`.

### Final Approval Gate

After all phases complete, present:

1. **Summary of what was built** (1 paragraph)
2. **All taste decisions** — present each with context, recommendation, and AskUserQuestion
3. **All user challenges** — present each with full context (what user said, what both recommend, why, cost of being wrong)
4. **Review report** from Phase 6

The user approves, rejects, or modifies each taste decision and user challenge.

## Usage

```bash
# Full auto-review
/autoplan I want to build a real-time collaboration tool for remote teams

# Auto-review existing blueprints
/autoplan resume
```

$ARGUMENTS
