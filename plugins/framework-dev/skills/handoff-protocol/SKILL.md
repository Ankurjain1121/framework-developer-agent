---
description: Use this skill when transitioning work between agents during Phase 5 (Execution). Defines the standard handoff document format, quality checklists, and context transfer protocols for multi-agent orchestration.
---

# Handoff Protocol for Multi-Agent Orchestration

This skill guides the Framework Developer Orchestrator on how to properly hand off work between different LLM sessions, ensuring continuity, quality, and traceability across multi-agent workflows.

---

## Why Handoffs Matter

When multiple agents work on a project:
- **Context is lost** between sessions
- **Assumptions differ** between LLMs
- **Quality varies** without standards
- **Integration fails** without clear contracts

The handoff protocol ensures each agent knows:
1. What was done before
2. What they need to do
3. What contracts to follow
4. How to signal completion

---

## Handoff Document Format

Create handoff files in `05-execution/handoffs/`:

```markdown
# Handoff: [Source Agent] → [Target Agent]

## Handoff ID: HO-001
**Created:** [ISO timestamp]
**Status:** PENDING | IN_PROGRESS | COMPLETED | BLOCKED

---

## Context Summary

### What Was Done
- [Brief summary of completed work]
- [Key decisions made]
- [Files created/modified]

### Current State
- Build status: PASSING | FAILING
- Tests: X passing, Y failing
- Lint: CLEAN | X warnings

---

## Your Tasks

### Primary Objective
[One sentence describing what this agent needs to accomplish]

### Specific Tasks
1. [ ] Task 1 - [Description]
2. [ ] Task 2 - [Description]
3. [ ] Task 3 - [Description]

### Out of Scope
- [Things this agent should NOT do]
- [Changes reserved for other agents]

---

## Contracts to Follow

### API Contracts
Read: `.framework-blueprints/03-api-planning/api-contracts.md`

Relevant endpoints:
| Endpoint | Method | Your Responsibility |
|----------|--------|---------------------|
| /api/users | GET | Implement handler |
| /api/users | POST | Implement validation |

### Code Patterns
- Use [pattern] for [purpose]
- Follow [convention] established in [file]

---

## Critical Details (DON'T FORGET)

### Environment Variables
- `DATABASE_URL` - PostgreSQL connection
- `JWT_SECRET` - For token signing

### Non-Standard Paths
- Auth is at `src/core/auth/`, not `src/auth/`

### API Quirks
- POST returns 201, not 200
- All dates are UTC ISO 8601

---

## Quality Checklist

Before marking handoff complete:
- [ ] All tasks completed
- [ ] No lint errors
- [ ] No type errors
- [ ] Tests pass
- [ ] Matches API contracts
- [ ] Documentation updated

---

## Handoff Notes

### Gotchas I Encountered
- [Issue and how you solved it]

### Recommendations for Next Agent
- [Suggestions based on what you learned]

### Open Questions
- [Questions that need user clarification]

---

## Completion

When done, update this file:
- Change status to COMPLETED
- Fill in completion timestamp
- Summarize what was accomplished
- List any deviations from plan

**Completed At:** [timestamp]
**Completed By:** [Agent/LLM name]
```

---

## Handoff States

| State | Meaning | Next Action |
|-------|---------|-------------|
| `PENDING` | Created, not started | Target agent picks up |
| `IN_PROGRESS` | Target agent working | Monitor progress |
| `COMPLETED` | All tasks done | Orchestrator reviews |
| `BLOCKED` | Cannot proceed | Resolve blocker |

---

## Creating a Handoff

### Step 1: Prepare Context
Before creating a handoff:
1. Read `00-project-state.json` for current status
2. Read `api-contracts.md` for relevant endpoints
3. Identify exactly what the next agent needs to know

### Step 2: Write the Handoff Document
Use the template above with:
- Unique ID (HO-001, HO-002, etc.)
- Specific tasks (not vague)
- Clear contracts to follow
- All critical details that could be forgotten

### Step 3: Update State
```json
{
  "handoffs": [
    {
      "id": "HO-001",
      "from": "Backend Agent",
      "to": "Frontend Agent",
      "status": "pending",
      "artifact": "05-execution/handoffs/HO-001-backend-to-frontend.md",
      "initiatedAt": "2025-01-03T10:00:00Z"
    }
  ]
}
```

### Step 4: Create Git Checkpoint
```bash
git add .
git commit -m "Handoff HO-001: Backend → Frontend"
git tag handoff-HO-001
```

---

## Receiving a Handoff

When picking up a handoff:

### Step 1: Read Everything
1. Read the handoff document completely
2. Read referenced API contracts
3. Read any referenced code files

### Step 2: Verify Understanding
Before starting work:
```markdown
I'm picking up handoff HO-001.

My understanding:
- I need to: [summary]
- I must follow: [contracts]
- Critical details: [list]

Is this correct? [Confirm with orchestrator]
```

### Step 3: Update Status
Change handoff status to `IN_PROGRESS`.

### Step 4: Complete the Checklist
Work through tasks, checking off as you go.

### Step 5: Complete Handoff
1. Update handoff document with completion info
2. Run quality checklist
3. Update state file
4. Create git checkpoint

---

## Quality Gates

### Before Sending Handoff
- [ ] Build passes
- [ ] Lint clean
- [ ] Tests pass
- [ ] API contracts verified
- [ ] Critical details documented

### Before Accepting Handoff
- [ ] Context fully understood
- [ ] Contracts read
- [ ] Questions asked
- [ ] Dependencies available

---

## Handoff Verification Protocol

The orchestrator verifies each handoff:

```markdown
## Handoff Verification: HO-001

### Deliverables Check
- [ ] All files listed exist
- [ ] Tests pass
- [ ] Build succeeds

### Contract Compliance
- [ ] Endpoints match api-contracts.md
- [ ] Schemas match specifications
- [ ] No unauthorized changes

### Documentation
- [ ] Handoff notes helpful
- [ ] Critical details accurate
- [ ] Next steps clear

### Verdict: APPROVED | NEEDS_REVISION
```

---

## Common Handoff Failures

| Failure | Cause | Prevention |
|---------|-------|------------|
| Wrong endpoints | Didn't read contracts | Always read api-contracts.md |
| Missing files | Forgot to commit | Verify with `git status` |
| Type errors | Different assumptions | Include type definitions |
| Integration fails | Incompatible interfaces | Test integration points |

---

## Handoff Anti-Patterns

### Don't Do This
- Vague task descriptions ("fix the auth")
- Missing contracts ("use REST best practices")
- Assumed knowledge ("you know the schema")
- No quality gate ("just push when done")

### Do This Instead
- Specific tasks ("implement POST /users with validation")
- Explicit contracts ("follow api-contracts.md section 3")
- Full context ("schema is defined in types/user.ts")
- Clear gates ("run `npm test` before marking complete")

---

## Integration with Phase 5

During Phase 5 (Execution):

1. **Start of agent session**: Read assigned handoff
2. **During work**: Update progress in handoff doc
3. **Blockers**: Update status to BLOCKED with reason
4. **Completion**: Fill completion section, run checklist
5. **Next handoff**: Create handoff for dependent agents

---

## Rollback Protocol

If a handoff must be reverted:

```bash
# Find the handoff checkpoint
git log --oneline | grep "handoff-HO-001"

# Revert to before the handoff
git revert <commit-hash>

# Update state
# Set handoff status back to PENDING
```

Document why the rollback was needed in the handoff file.

---

## Quick Reference

### Creating Handoff
1. Prepare context (read state, contracts)
2. Write handoff document
3. Update state file
4. Git checkpoint

### Receiving Handoff
1. Read handoff completely
2. Confirm understanding
3. Update status to IN_PROGRESS
4. Complete tasks
5. Run quality checklist
6. Complete handoff

### Verifying Handoff
1. Check all deliverables exist
2. Verify contract compliance
3. Run tests
4. Approve or request revision
