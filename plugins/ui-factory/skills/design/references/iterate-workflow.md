# Iterate Workflow

Process for improving existing UI based on user feedback, analytics, or observations.

---

## Input Types

| Type | Example | How to Use |
|------|---------|------------|
| User quotes | "I couldn't find the save button" | Map to affected pass |
| Analytics | 40% drop-off at step 3 | Identify friction point |
| Support tickets | "How do I export?" | Find missing affordance |
| Session recordings | User clicks wrong element | Identify affordance issue |
| A/B test results | Variant B +15% conversion | Understand what worked |

---

## Step 1: Categorize Feedback

Map each piece of feedback to the affected UX pass:

| Feedback Pattern | Affected Pass |
|------------------|---------------|
| "I don't understand what this is for" | Pass 1: Mental Model |
| "I can't find X" | Pass 2: Information Architecture |
| "I didn't know I could click that" | Pass 3: Affordances |
| "There are too many options" | Pass 4: Cognitive Load |
| "I didn't know if it worked" | Pass 5: State Design |
| "I got lost / stuck" | Pass 6: Flow Integrity |
| "I can't use keyboard" | Pass 7: Accessibility |
| "It doesn't work on my phone" | Pass 8: Responsive |
| "It feels slow / clunky" | Pass 9: Motion |

---

## Step 2: Prioritize by Impact

| Priority | Criteria |
|----------|----------|
| P0 | Blocks core task completion |
| P1 | Affects >30% of users |
| P2 | Causes significant confusion |
| P3 | Minor friction, nice to fix |

---

## Step 3: Re-run Affected Passes

Don't redesign everything. Only re-analyze the affected passes:

```markdown
## Re-run: Pass [N] - [Name]

### Current State
[What the UI does now]

### Problem
[Why it's confusing/broken]

### User Expectation
[What user expected instead]

### Fix
[Specific change to make]

### Validation
[How to know it's fixed]
```

---

## Step 4: Preserve What Works

Before making changes, identify:

| Keep | Reason |
|------|--------|
| [Element/behavior] | [Why it's working] |
| [Element/behavior] | [User feedback that it's good] |

**Rule:** Don't break working things while fixing broken things.

---

## Step 5: Generate Implementation Checklist

```markdown
## Implementation Checklist

### High Priority (P0-P1)
- [ ] [Change 1]: [File] - [Specific edit]
- [ ] [Change 2]: [File] - [Specific edit]

### Medium Priority (P2)
- [ ] [Change 3]: [File] - [Specific edit]

### Low Priority (P3)
- [ ] [Change 4]: [File] - [Specific edit]
```

---

## Output Template

```markdown
# Iteration Spec: [Component/Page]

## Feedback Summary
| Source | Feedback | Pass | Priority |
|--------|----------|------|----------|
| [Who] | [What they said] | [N] | [P0-3] |

## Passes to Re-run
- Pass [N]: [Reason]
- Pass [N]: [Reason]

## Detailed Fixes

### Pass [N] Re-run
- Current: [Description]
- Problem: [Description]
- Fix: [Description]

## What to Keep
- [Element]: [Why]

## Implementation
- [ ] [Task]
```

---

## Common Iteration Patterns

| Pattern | Typical Fix |
|---------|-------------|
| "Can't find X" | Move to primary visibility, add to navigation |
| "Too many options" | Group, hide advanced, add defaults |
| "Didn't know it worked" | Add success state, confirmation |
| "Accidentally clicked wrong thing" | Increase spacing, add confirmation |
| "Keyboard doesn't work" | Add tabindex, keyboard handlers |
