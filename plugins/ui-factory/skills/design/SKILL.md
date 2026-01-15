---
name: design
description: Complete design thinking pipeline for UIs. Transforms rough ideas into PRDs, runs 9-pass UX analysis, audits existing UIs, and iterates based on feedback. Prevents "pretty but unusable" designs.
version: 2.0.0
updated: 2026-01-16
---

# /design

Complete design thinking pipeline that ensures Claude builds better UIs by forcing structured analysis before any visual work. Now with audit, iterate, and specialized sub-skills.

```
Rough Idea → PRD (1-7) → UX Spec (9 passes) → /frontend-design
     OR
Existing UI → Audit → Issues Report → Iterate → Improvements
```

## Subcommands

| Command | Purpose |
|---------|---------|
| `/design` | Run full pipeline (idea → PRD → UX → build) |
| `/design init` | Generate PRD sections 1-7 from rough idea |
| `/design ux` | Generate UX spec from existing PRD (9 passes) |
| `/design build` | Hand off UX spec to `/frontend-design` skill |
| `/design audit` | **NEW:** Analyze existing UI and generate issues report |
| `/design iterate` | **NEW:** Improve UI based on user feedback |
| `/design a11y` | **NEW:** Accessibility-focused audit and fixes |
| `/design motion` | **NEW:** Animation and micro-interaction design |
| `/design tokens` | **NEW:** Extract or create design system tokens |
| `/design components` | **NEW:** Best-practice component patterns |

---

## `/design` (Full Pipeline)

Runs all stages automatically:
1. Ask for rough idea if not provided
2. Generate PRD → `.claude/design/PRD.md`
3. Generate UX spec (9 passes) → `.claude/design/UX-spec.md`
4. Invoke `/frontend-design` with UX context

---

## `/design init` - Rough Idea to PRD

**Input:** Rough MVP idea (vague, incomplete, "vibe-level")

**Role:** Senior product thinker turning rough idea into demo-grade PRD.

**Output:** `.claude/design/PRD.md` with sections 1-7:

### Section 1: One-Sentence Problem
```
[User] struggles to [do X] because [reason], resulting in [impact].
```

### Section 2: Demo Goal
- What must work for demo success
- What outcome the demo communicates
- Non-goals (intentionally out of scope)

### Section 3: Target User (Role-Based)
- Role / context
- Skill level
- Key constraint (time, knowledge, access)

No personas or demographics.

### Section 4: Core Use Case (Happy Path)
- Start condition
- Step-by-step flow (numbered)
- End condition

If this flow works, the demo works.

### Section 5: Functional Decisions

| ID | Function | Notes |
|----|----------|-------|
| F1 | [Capability] | [Notes] |

Phrase as capabilities, not implementation. No "nice-to-haves".

### Section 6: UX Decisions

**6.1 Entry Point:** How user starts, what they see first
**6.2 Inputs:** What user provides
**6.3 Outputs:** What user receives, in what form
**6.4 Feedback & States:** Loading, success, failure, partial
**6.5 Errors:** Invalid input, system failure, user does nothing

### Section 7: Data & Logic

**7.1 Inputs:** User / API / static / generated
**7.2 Processing:** High-level only (input → transform → output)
**7.3 Outputs:** UI only / temp stored / logged

**Rules:**
- Optimize for speed + clarity
- Label assumptions explicitly
- If extremely vague, ask ONE clarifying question max, then proceed

See `references/prd-template.md` for detailed templates.

---

## `/design ux` - PRD to UX Spec

**Input:** `.claude/design/PRD.md` (or user-provided PRD)

**Output:** `.claude/design/UX-spec.md` with 9 mandatory passes

### THE IRON LAW

```
NO VISUAL SPECS UNTIL ALL 9 PASSES COMPLETE
```

**Not negotiable:**
- Don't mention colors, typography, spacing until Pass 9 done
- Don't describe screen layouts until IA is explicit
- Don't design components until affordances mapped

### Pass 1: Mental Model Alignment
**Question:** "What does the user think is happening?"

Output:
- Primary user intent (one sentence)
- Likely misconceptions
- UX principle to reinforce/correct

### Pass 2: Information Architecture
**Question:** "What exists, and how is it organized?"

Output:
- ALL concepts user will encounter
- Grouped structure
- Each concept: Primary / Secondary / Hidden

### Pass 3: Affordances & Action Clarity
**Question:** "What actions are obvious without explanation?"

Output:
- What is clickable?
- What looks editable?
- What looks read-only?
- What looks final vs in-progress?

### Pass 4: Cognitive Load & Decision Minimization
**Question:** "Where will the user hesitate?"

Output:
- Moments of choice (decisions required)
- Moments of uncertainty (unclear what to do)
- Moments of waiting (system processing)
- Simplifications: collapse decisions, delay complexity, introduce defaults

### Pass 5: State Design & Feedback
**Question:** "How does the system talk back?"

For EACH major element:
| State | User Sees | User Understands | User Can Do |
|-------|-----------|------------------|-------------|
| Empty | | | |
| Loading | | | |
| Success | | | |
| Partial | | | |
| Error | | | |

### Pass 6: Flow Integrity Check
**Question:** "Does this feel inevitable?"

Output:
- Flow risks (where could users get lost?)
- Visibility decisions (must be visible vs can be implied)
- UX constraints (hard rules for visual phase)

### Pass 7: Accessibility (a11y)
**Question:** "Can everyone use this?"

Output:
- Keyboard navigation map (tab order, focus traps)
- Screen reader announcements (what gets read, when)
- Color contrast requirements (WCAG AA minimum)
- ARIA patterns needed
- Touch target sizes (44x44 minimum)
- Focus indicators

See `references/a11y-checklist.md` for detailed checklist.

### Pass 8: Responsive Strategy
**Question:** "How does this adapt to different screens?"

Output:
- Breakpoint decisions (mobile-first vs desktop-first)
- Content priority per viewport
- Navigation pattern changes (hamburger, tabs, sidebar)
- Touch vs mouse interactions
- Typography scaling
- Layout reflow rules

### Pass 9: Motion & Delight
**Question:** "How does this come alive?"

Output:
- Entry animations (what animates in, from where)
- Exit animations (what animates out, to where)
- Micro-interactions (hover, click, success feedback)
- Loading states animation
- State transitions (smooth or instant)
- Performance budget (max duration, complexity)

See `references/motion-patterns.md` for patterns.

See `references/ux-passes.md` for detailed methodology.

---

## `/design build` - UX Spec to Frontend

**Input:** `.claude/design/UX-spec.md`

**Action:** Invoke `/frontend-design` skill with full UX context

**Context passed to `/frontend-design`:**
- Mental model (what user expects)
- Information architecture (all concepts, groupings)
- Affordances (clickable, editable, read-only signals)
- Cognitive load analysis (friction points, defaults)
- State design (empty/loading/success/error per element)
- Flow integrity (risks, visibility decisions, constraints)
- Accessibility requirements
- Responsive strategy
- Motion specifications

**Result:** Production-grade UI built with proper design thinking already done.

---

## `/design audit` - Analyze Existing UI

**Input:**
- Page or component path (e.g., `src/components/dashboard`)
- OR description of current UI behavior
- OR screenshot description

**Process:**
1. Read existing component code
2. Run abbreviated 9-pass analysis
3. Identify UX issues by pass
4. Prioritize by severity (Critical / Major / Minor)
5. Generate actionable improvement list

**Output:** `.claude/design/audit-report.md`

```markdown
# UX Audit Report: [Component/Page Name]

## Summary
- Critical Issues: X
- Major Issues: Y
- Minor Issues: Z

## Issues by Pass

### Mental Model Issues
- [Issue]: [Description] → [Fix]

### Information Architecture Issues
- [Issue]: [Description] → [Fix]

### Affordance Issues
- [Issue]: [Description] → [Fix]

... (all 9 passes)

## Priority Actions
1. [Highest impact fix]
2. [Second highest]
3. [Third highest]
```

**Use case:** "Make this project's UI better"

See `references/audit-checklist.md` for criteria.

---

## `/design iterate` - Improve Based on Feedback

**Input:**
- User feedback (quotes, complaints, observations)
- Analytics data (drop-off points, rage clicks)
- Support tickets
- Current component/page reference

**Process:**
1. Categorize feedback by affected pass
2. Re-run affected passes only
3. Generate targeted improvements
4. Preserve what's working

**Output:** `.claude/design/iteration-spec.md`

```markdown
# Iteration Spec: [Component/Page Name]

## Feedback Analysis
| Feedback | Affected Pass | Severity |
|----------|---------------|----------|
| "I don't know where to click" | Pass 3 (Affordances) | Critical |
| "Loading takes forever" | Pass 5 (State Design) | Major |

## Targeted Fixes

### Pass 3: Affordances (Re-run)
- Current: [What it does now]
- Problem: [Why it's confusing]
- Fix: [Specific change]

### Pass 5: State Design (Re-run)
- Current: [What it does now]
- Problem: [Why it's frustrating]
- Fix: [Specific change]

## Implementation Checklist
- [ ] Fix 1: [Description]
- [ ] Fix 2: [Description]
```

**Use case:** "Users say the form is confusing"

See `references/iterate-workflow.md` for process.

---

## `/design a11y` - Accessibility Audit

**Input:** Component or page path

**Process:**
1. Analyze semantic HTML structure
2. Check keyboard navigation
3. Verify ARIA usage
4. Test color contrast (via code analysis)
5. Check focus management
6. Validate form accessibility

**Output:** `.claude/design/a11y-report.md`

```markdown
# Accessibility Report: [Component Name]

## WCAG 2.1 AA Compliance

### Perceivable
- [ ] 1.1.1 Non-text Content: [Status]
- [ ] 1.3.1 Info and Relationships: [Status]
- [ ] 1.4.3 Contrast (Minimum): [Status]

### Operable
- [ ] 2.1.1 Keyboard: [Status]
- [ ] 2.4.3 Focus Order: [Status]
- [ ] 2.4.7 Focus Visible: [Status]

### Understandable
- [ ] 3.1.1 Language of Page: [Status]
- [ ] 3.2.1 On Focus: [Status]
- [ ] 3.3.1 Error Identification: [Status]

### Robust
- [ ] 4.1.1 Parsing: [Status]
- [ ] 4.1.2 Name, Role, Value: [Status]

## Issues Found
| Issue | WCAG | Severity | Fix |
|-------|------|----------|-----|
| [Description] | [Criterion] | [Level] | [Solution] |

## Keyboard Navigation Map
[Tab order diagram]

## Screen Reader Flow
[Announcement sequence]
```

See `references/a11y-checklist.md` for complete checklist.

---

## `/design motion` - Animation Design

**Input:**
- UX spec or component
- OR description of desired interaction

**Process:**
1. Identify animation opportunities
2. Choose appropriate patterns
3. Define timing and easing
4. Consider reduced motion preferences
5. Set performance budget

**Output:** `.claude/design/motion-spec.md`

```markdown
# Motion Specification: [Component Name]

## Animation Inventory

### Entry Animations
| Element | Animation | Duration | Easing | Delay |
|---------|-----------|----------|--------|-------|
| Modal | Fade + Scale | 200ms | ease-out | 0ms |
| Items | Stagger fade | 150ms | ease-out | 50ms each |

### Exit Animations
| Element | Animation | Duration | Easing |
|---------|-----------|----------|--------|
| Modal | Fade + Scale down | 150ms | ease-in |

### Micro-interactions
| Trigger | Response | Duration |
|---------|----------|----------|
| Button hover | Scale 1.02 | 100ms |
| Button click | Scale 0.98 | 50ms |
| Success | Checkmark draw | 300ms |

### Loading States
| State | Animation | Notes |
|-------|-----------|-------|
| Skeleton | Shimmer | 1.5s loop |
| Spinner | Rotate | 1s loop |

## Reduced Motion Fallbacks
- All animations → instant transitions
- Shimmer → static gray
- Spinners → static indicator

## Performance Budget
- Total animation time: < 500ms
- Concurrent animations: < 3
- Use transform/opacity only (no layout thrash)
```

See `references/motion-patterns.md` for patterns library.

---

## `/design tokens` - Design System Tokens

**Input:**
- Existing codebase (reads tailwind.config.js, CSS variables)
- OR brand guidelines description
- OR "generate from scratch"

**Process:**
1. Extract existing tokens (if any)
2. Identify gaps
3. Generate complete token set
4. Output in multiple formats

**Output:** `.claude/design/tokens.md` + optional config files

```markdown
# Design Tokens: [Project Name]

## Colors

### Brand
| Token | Value | Usage |
|-------|-------|-------|
| --color-primary | #3B82F6 | Primary actions, links |
| --color-primary-hover | #2563EB | Primary hover state |

### Semantic
| Token | Value | Usage |
|-------|-------|-------|
| --color-success | #10B981 | Success states |
| --color-error | #EF4444 | Error states |
| --color-warning | #F59E0B | Warning states |

### Neutral
| Token | Value | Usage |
|-------|-------|-------|
| --color-background | #FFFFFF | Page background |
| --color-surface | #F9FAFB | Card backgrounds |
| --color-border | #E5E7EB | Borders, dividers |

## Typography

| Token | Value | Usage |
|-------|-------|-------|
| --font-size-xs | 0.75rem | Helper text |
| --font-size-sm | 0.875rem | Body small |
| --font-size-base | 1rem | Body |
| --font-size-lg | 1.125rem | Body large |
| --font-size-xl | 1.25rem | H4 |
| --font-size-2xl | 1.5rem | H3 |
| --font-size-3xl | 1.875rem | H2 |
| --font-size-4xl | 2.25rem | H1 |

## Spacing

| Token | Value | Usage |
|-------|-------|-------|
| --space-1 | 0.25rem | Tight spacing |
| --space-2 | 0.5rem | Element padding |
| --space-3 | 0.75rem | Small gaps |
| --space-4 | 1rem | Standard gaps |
| --space-6 | 1.5rem | Section spacing |
| --space-8 | 2rem | Large gaps |

## Border Radius

| Token | Value | Usage |
|-------|-------|-------|
| --radius-sm | 0.25rem | Small elements |
| --radius-md | 0.375rem | Buttons, inputs |
| --radius-lg | 0.5rem | Cards |
| --radius-full | 9999px | Pills, avatars |

## Shadows

| Token | Value | Usage |
|-------|-------|-------|
| --shadow-sm | 0 1px 2px rgba(0,0,0,0.05) | Subtle elevation |
| --shadow-md | 0 4px 6px rgba(0,0,0,0.1) | Cards |
| --shadow-lg | 0 10px 15px rgba(0,0,0,0.1) | Modals |

## Breakpoints

| Token | Value | Usage |
|-------|-------|-------|
| --breakpoint-sm | 640px | Small tablets |
| --breakpoint-md | 768px | Tablets |
| --breakpoint-lg | 1024px | Laptops |
| --breakpoint-xl | 1280px | Desktops |
```

See `references/token-extraction.md` for extraction process.

---

## `/design components` - Component Patterns

**Input:** Description of component need (e.g., "multi-step form", "data table with sorting")

**Process:**
1. Identify component category
2. Select best-practice pattern
3. Adapt to project's design system
4. Include all states and variants

**Output:** Component specification with:
- Structure (hierarchy, slots)
- States (default, hover, focus, disabled, error)
- Variants (sizes, styles)
- Accessibility requirements
- Example usage

### Available Patterns

**Forms:**
- Single-field forms
- Multi-step wizards
- Inline editing
- Search with autocomplete

**Data Display:**
- Data tables (sortable, filterable)
- Card grids
- Lists (simple, complex)
- Empty states

**Navigation:**
- Tabs
- Breadcrumbs
- Pagination
- Sidebar navigation

**Feedback:**
- Toasts/Notifications
- Progress indicators
- Skeleton loaders
- Error states

**Overlays:**
- Modals/Dialogs
- Drawers/Sheets
- Popovers
- Tooltips

See `references/component-patterns.md` for full pattern library.

---

## File Structure

```
.claude/design/
├── PRD.md              # Stage 1 output
├── UX-spec.md          # Stage 2 output (9 passes)
├── audit-report.md     # Audit output
├── iteration-spec.md   # Iterate output
├── a11y-report.md      # Accessibility output
├── motion-spec.md      # Motion output
├── tokens.md           # Tokens output
└── component-spec.md   # Component pattern output
```

---

## Context Awareness

### Design System Detection

Before any design work, automatically check for:

1. **Tailwind config:** `tailwind.config.js` or `tailwind.config.ts`
   - Extract color palette
   - Extract spacing scale
   - Extract breakpoints

2. **CSS variables:** `:root` definitions in global CSS
   - Map existing tokens

3. **Component library:** Check for shadcn, Radix, MUI, etc.
   - Match existing patterns

4. **Existing components:** Scan `src/components/ui/`
   - Identify reusable elements
   - Match naming conventions

### Codebase Pattern Detection

Before designing new components:

1. **Read existing components** in the same category
2. **Identify patterns:** How do existing forms work? How are errors shown?
3. **Match conventions:** Don't introduce new patterns unnecessarily
4. **Reuse:** Suggest existing components when applicable

---

## Red Flags - Stop and Restart

If you catch yourself doing any of these during `/design ux`, STOP:

| Violation | What You're Skipping |
|-----------|---------------------|
| Describing colors/fonts | All foundational passes |
| "The main screen shows..." | Pass 1-2 (mental model, IA) |
| Designing components before actions mapped | Pass 3 (affordances) |
| No friction point analysis | Pass 4 (cognitive load) |
| States only in component specs | Pass 5 (holistic state design) |
| No "where could they fail?" | Pass 6 (flow integrity) |
| No keyboard navigation plan | Pass 7 (accessibility) |
| No mobile consideration | Pass 8 (responsive) |
| Static mockups with no transitions | Pass 9 (motion) |

---

## When to Use Each Command

| Situation | Command |
|-----------|---------|
| New feature from scratch | `/design` (full pipeline) |
| Have requirements, need UX spec | `/design ux` |
| Existing UI needs improvement | `/design audit` |
| Users complaining about UX | `/design iterate` |
| Need to check accessibility | `/design a11y` |
| Adding animations | `/design motion` |
| Setting up design system | `/design tokens` |
| Need component best practices | `/design components` |
