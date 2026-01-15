# UX Audit Checklist

A systematic checklist for auditing existing UIs using the 9-pass methodology.

---

## How to Use

1. Read the component/page code
2. Run through each section below
3. Mark issues with severity: Red Critical | Yellow Major | Green Minor
4. Generate prioritized action list

---

## Pass 1: Mental Model Check

| Check | Question | Issue If... |
|-------|----------|-------------|
| User intent | What does user think this does? | Unclear or misleading |
| Terminology | Do labels match user expectations? | Jargon or confusion |
| Metaphor | Does the UI metaphor hold? | Breaks in unexpected places |

---

## Pass 2: Information Architecture Check

| Check | Question | Issue If... |
|-------|----------|-------------|
| Hierarchy | Is most important info prominent? | Buried or equal weight |
| Grouping | Are related items together? | Scattered across UI |
| Naming | Are labels descriptive? | Vague or inconsistent |

---

## Pass 3: Affordance Check

| Check | Question | Issue If... |
|-------|----------|-------------|
| Clickable | Do interactive elements look clickable? | Flat or ambiguous |
| Editable | Is editable content obvious? | Looks read-only |
| State | Can user tell current state? | No visual distinction |

---

## Pass 4: Cognitive Load Check

| Check | Question | Issue If... |
|-------|----------|-------------|
| Choices | How many decisions per screen? | More than 3-4 |
| Defaults | Are good defaults provided? | User must choose everything |
| Progressive | Is complexity hidden until needed? | All options visible |

---

## Pass 5: State Design Check

| Check | Question | Issue If... |
|-------|----------|-------------|
| Empty | What shows when no data? | Blank or confusing |
| Loading | How is loading communicated? | No feedback |
| Error | How are errors shown? | Generic or missing |
| Success | How is success confirmed? | No acknowledgment |

---

## Pass 6: Flow Integrity Check

| Check | Question | Issue If... |
|-------|----------|-------------|
| Entry | Is starting point clear? | User doesn't know where to begin |
| Progress | Can user see where they are? | No breadcrumbs or indicators |
| Exit | Is completing the flow obvious? | No clear finish |
| Recovery | Can user undo or go back? | No escape routes |

---

## Pass 7: Accessibility Check

| Check | Question | Issue If... |
|-------|----------|-------------|
| Keyboard | Can all actions be done with keyboard? | Mouse-only interactions |
| Screen reader | Are elements labeled for AT? | Missing aria labels |
| Contrast | Is text readable? | Below 4.5:1 ratio |
| Focus | Is focus visible and logical? | Hidden or jumping |

---

## Pass 8: Responsive Check

| Check | Question | Issue If... |
|-------|----------|-------------|
| Mobile | Does it work on small screens? | Overflow or hidden content |
| Touch | Are touch targets large enough? | Below 44x44px |
| Orientation | Does rotation work? | Layout breaks |

---

## Pass 9: Motion Check

| Check | Question | Issue If... |
|-------|----------|-------------|
| Feedback | Do interactions provide feedback? | Silent or delayed |
| Purpose | Do animations serve a purpose? | Decorative only |
| Performance | Are animations smooth? | Janky or slow |
| Reduced | Is prefers-reduced-motion respected? | Forces animation |

---

## Severity Guide

| Level | Definition | Examples |
|-------|------------|----------|
| Critical | Blocks user from completing task | Can't submit form, hidden CTA |
| Major | Causes significant confusion/friction | Unclear labels, missing states |
| Minor | Suboptimal but usable | Inconsistent spacing, minor a11y |

---

## Output Template

```markdown
# UX Audit: [Component Name]

## Summary
- Critical: X
- Major: Y
- Minor: Z

## Issues

### [Pass Name]
| Severity | Issue | Location | Fix |
|----------|-------|----------|-----|
| Critical | [Description] | [Where] | [How] |

## Priority Actions
1. [Most critical fix]
2. [Second most critical]
3. [Third]
```
