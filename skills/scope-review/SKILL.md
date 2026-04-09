---
description: Scope challenge and mode selection for framework planning. Challenges premises, evaluates complexity, enforces completeness, and manages 4 scope modes (Expansion, Selective, Hold, Reduction).
---

# Scope Review Skill

## Purpose

Ensure every plan is built on valid premises, appropriately scoped, and challenged before implementation begins. This skill combines the best of CEO-level strategic thinking with engineering-level scope discipline.

---

## Premise Challenge (NEVER skip, NEVER auto-decide)

Before any planning work, challenge the fundamental premise:

### Questions to Answer:
1. **Is this the right problem to solve?** Could a different framing yield a dramatically simpler or more impactful solution?
2. **What is the actual user/business outcome?** Is the plan the most direct path, or is it solving a proxy problem?
3. **What would happen if we did nothing?** Real pain point or hypothetical one?
4. **What are the unstated assumptions?** List every assumption the plan relies on. For each: is it verified or just believed?

### Premise Validation Format:
```
PREMISE: [stated or implied assumption]
STATUS: VALID | CHALLENGED | NEEDS_VERIFICATION
EVIDENCE: [source or reasoning]
RISK IF WRONG: [consequence]
```

Write findings to `01-discovery/premise-challenge.md`.

---

## Scope Modes

### A) SCOPE EXPANSION
- Dream big. Find the 10x version.
- Push scope UP where 2x effort yields 10x value.
- Every expansion is the user's decision via AskUserQuestion.
- Posture: enthusiastic recommendation.

### B) SELECTIVE EXPANSION (default for /autoplan)
- Hold current scope as baseline — make it bulletproof.
- Surface every expansion opportunity individually for cherry-picking.
- Posture: neutral — state effort, risk, let user decide.

### C) HOLD SCOPE
- The plan's scope is accepted. Make it bulletproof.
- No expansion, no reduction. Maximum rigor.
- Posture: relentless quality enforcement.

### D) SCOPE REDUCTION
- Find the minimum viable version. Cut everything non-essential.
- Separate "must ship together" from "nice to ship together."
- Posture: surgical precision.

**Critical rule:** Once selected, COMMIT. Do not silently drift toward a different mode.

---

## Scope Challenge (Engineering Rigor)

Before implementation planning, answer these:

### 1. Existing Code Leverage
What existing code already partially or fully solves each sub-problem? Map every sub-problem to existing code. Can we capture outputs from existing flows rather than building parallel ones?

### 2. Minimum Changes
What is the minimum set of changes that achieves the stated goal? Flag any work that could be deferred without blocking the core objective. Be ruthless about scope creep.

### 3. Complexity Check
If the plan touches more than 8 files or introduces more than 2 new classes/services, treat that as a smell. Challenge whether the same goal can be achieved with fewer moving parts.

**If triggered:** Proactively recommend scope reduction via AskUserQuestion. Explain what's overbuilt, propose minimal version, ask whether to reduce or proceed.

### 4. Search Check
For each architectural pattern, infrastructure component, or approach the plan introduces:
- Does the runtime/framework have a built-in? Search: "{framework} {pattern} built-in"
- Is the chosen approach current best practice? Search: "{pattern} best practice the current year"
- Are there known footguns? Search: "{framework} {pattern} pitfalls"

### 5. Completeness Check
Is the plan doing the complete version or a shortcut? With AI-assisted coding, the cost of completeness (100% test coverage, full edge case handling, complete error paths) is 10-100x cheaper. Recommend the complete version.

### 6. Distribution Check
If the plan introduces a new artifact type (CLI binary, library package, container image, mobile app), does it include the build/publish pipeline? Code without distribution is code nobody can use.

---

## Implementation Alternatives (MANDATORY)

Every plan MUST consider alternatives before committing:

```
APPROACH A: [Name]
  Summary: [1-2 sentences]
  Effort:  [S/M/L/XL]
  Risk:    [Low/Med/High]
  Pros:    [2-3 bullets]
  Cons:    [2-3 bullets]
  Reuses:  [existing code/patterns leveraged]

APPROACH B: [Name]
  ...

APPROACH C: [Name] (optional)
  ...
```

Rules:
- At least 2 approaches required. 3 preferred for non-trivial plans.
- One approach must be "minimal viable" (fewest files, smallest diff).
- One approach must be "ideal architecture" (best long-term trajectory).
- If only one approach exists, explain concretely why alternatives were eliminated.
- RECOMMENDATION with one-line reason mapped to engineering preferences.

---

## Dream State Mapping

Describe the ideal end state 12 months from now:

```
  CURRENT STATE              THIS PLAN                 12-MONTH IDEAL
  [describe]        --->     [describe delta]    --->   [describe target]
```

Does this plan move toward that state or away from it?

---

## Mode-Specific Analysis

### SCOPE EXPANSION:
1. **10x check:** What's 10x more ambitious for 2x effort? Describe concretely.
2. **Platonic ideal:** Best engineer, unlimited time, perfect taste — what would this look like?
3. **Delight opportunities:** 5+ adjacent 30-minute improvements that would make users think "oh nice, they thought of that."
4. **Opt-in ceremony:** Present each expansion as its own AskUserQuestion. Options: A) Add to scope, B) Defer to TODOS.md, C) Skip.

### SELECTIVE EXPANSION:
1. Run HOLD SCOPE analysis first
2. Run expansion scan (10x check, delight opportunities, platform potential)
3. **Cherry-pick ceremony:** Present each expansion individually. Neutral posture. Options: A) Add to scope, B) Defer, C) Skip.

### HOLD SCOPE:
1. Complexity check (8+ files = smell)
2. Minimum set of changes
3. Full edge case mapping

### SCOPE REDUCTION:
1. Ruthless cut: absolute minimum that ships value
2. Separate "must ship together" from "nice to ship together"
3. Everything else deferred to TODOS.md

---

## Output

### Required Files:
- `01-discovery/premise-challenge.md` — Premise validation
- `01-discovery/alternatives.md` — Implementation approaches
- `01-discovery/dream-state.md` — Trajectory mapping
- `03-api-planning/scope-challenge.md` — Engineering scope analysis

### "NOT in scope" Section (MANDATORY)
Every plan review MUST produce a "NOT in scope" section listing work that was considered and explicitly deferred, with one-line rationale for each item.

### "What already exists" Section
Map every sub-problem to existing code that partially or fully solves it.
