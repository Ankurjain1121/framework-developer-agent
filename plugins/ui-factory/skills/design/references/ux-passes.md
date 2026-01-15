# UX Passes Methodology

The 9 passes that prevent "pretty but unusable" designs.

---

## The Iron Law

```
NO VISUAL SPECS UNTIL ALL 9 PASSES COMPLETE
```

**Why this matters:**
- Skipping passes produces specs that need redesign
- The 9 passes ARE the shortcut
- Urgency is a trap - passes take 5 minutes, fixing bad UX takes days

**No exceptions for:**
- "I'm in a hurry"
- "Just give me screens"
- "Skip the analysis"
- "I know what I want"
- "The PRD is simple enough"

---

## Pass Execution Order

```
Pass 1: Mental Model
    ↓
Pass 2: Information Architecture
    ↓
Pass 3: Affordances
    ↓
Pass 4: Cognitive Load
    ↓
Pass 5: State Design
    ↓
Pass 6: Flow Integrity
    ↓
Pass 7: Accessibility
    ↓
Pass 8: Responsive Strategy
    ↓
Pass 9: Motion & Delight
    ↓
THEN: Visual Specifications
```

Each pass produces required outputs before the next begins.

---

## Pass 1: Mental Model Alignment

**Designer mindset:** "What does the user think is happening?"

### Questions to Force
- What does the user believe this system does?
- What are they trying to accomplish in one sentence?
- What wrong mental models are likely?

### Required Output
```markdown
## Pass 1: Mental Model

**Primary user intent:** [One sentence - what user thinks they're doing]

**Likely misconceptions:**
- [Misconception 1] - why they might think this
- [Misconception 2] - why they might think this

**UX principle to reinforce/correct:** [Specific principle to apply]
```

### Example
```markdown
## Pass 1: Mental Model

**Primary user intent:** "I want to describe my app idea and have someone build it"

**Likely misconceptions:**
- "This will generate final production code" - user expects finished product
- "I just describe once and it's done" - user expects no iteration
- "The output is the UI" - user doesn't expect intermediate documents

**UX principle to reinforce/correct:** Progressive disclosure - show each stage clearly so user understands the pipeline
```

---

## Pass 2: Information Architecture

**Designer mindset:** "What exists, and how is it organized?"

### Actions to Force
1. Enumerate ALL concepts user will encounter
2. Group into logical buckets
3. Classify each as: Primary / Secondary / Hidden

### Required Output
```markdown
## Pass 2: Information Architecture

**All user-visible concepts:**
- [Concept 1]
- [Concept 2]
- ...

**Grouped structure:**

### [Group Name]
| Concept | Visibility | Rationale |
|---------|------------|-----------|
| [Concept] | Primary/Secondary/Hidden | [Why] |
```

### Why This Pass Matters
This is where most AI UX attempts fail. Without explicit IA, visual specs are disorganized.

---

## Pass 3: Affordances & Action Clarity

**Designer mindset:** "What actions are obvious without explanation?"

### Explicit Decisions Required
- What is clickable?
- What looks editable?
- What looks like output (read-only)?
- What looks final vs in-progress?

### Required Output
```markdown
## Pass 3: Affordances

| Element | Action | Visual Signal |
|---------|--------|---------------|
| [Element] | [What user can do] | [What makes it obvious] |

**Affordance rules:**
- If user sees X, they should assume Y
- [Rule 2]
- [Rule 3]
```

### Note
No visuals required—just clarity on what signals what.

---

## Pass 4: Cognitive Load & Decision Minimization

**Designer mindset:** "Where will the user hesitate?"

### Identify
- Moments of choice (decisions required)
- Moments of uncertainty (unclear what to do)
- Moments of waiting (system processing)

### Apply
- Collapse decisions (fewer choices)
- Delay complexity (progressive disclosure)
- Introduce defaults (reduce decision burden)

### Required Output
```markdown
## Pass 4: Cognitive Load

**Friction points:**
| Moment | Type | Location | Simplification |
|--------|------|----------|----------------|
| [When] | Choice/Uncertainty/Wait | [Where] | [How to reduce] |

**Defaults introduced:**
| Default | Value | Rationale |
|---------|-------|-----------|
| [Setting] | [Default value] | [Why this default] |
```

---

## Pass 5: State Design & Feedback

**Designer mindset:** "How does the system talk back?"

### For EACH Major Element
Enumerate states:
- Empty
- Loading
- Success
- Partial (incomplete data)
- Error

### For Each State Answer
- What does the user see?
- What do they understand?
- What can they do next?

### Required Output
```markdown
## Pass 5: State Design

### [Element/Screen Name]

| State | User Sees | User Understands | User Can Do |
|-------|-----------|------------------|-------------|
| Empty | [Visual] | [Mental state] | [Available actions] |
| Loading | [Visual] | [Mental state] | [Available actions] |
| Success | [Visual] | [Mental state] | [Available actions] |
| Partial | [Visual] | [Mental state] | [Available actions] |
| Error | [Visual] | [Mental state] | [Available actions] |
```

### Why This Pass Matters
This prevents "dead UX"—screens with no feedback.

---

## Pass 6: Flow Integrity Check

**Designer mindset:** "Does this feel inevitable?"

### Final Sanity Check
- Where could users get lost?
- Where would a first-time user fail?
- What must be visible vs can be implied?

### Required Output
```markdown
## Pass 6: Flow Integrity

**Flow risks:**
| Risk | Location | Mitigation |
|------|----------|------------|
| [What could go wrong] | [Where] | [Guardrail/Nudge] |

**Visibility decisions:**
- **Must be visible:** [List]
- **Can be implied:** [List]

**UX constraints for visual phase:**
- [Hard rule 1]
- [Hard rule 2]
```

---

## Pass 7: Accessibility (a11y)

**Designer mindset:** "Can everyone use this?"

### Questions to Force
- Can keyboard-only users navigate this?
- What will screen readers announce?
- Is color contrast sufficient?
- Are touch targets large enough?
- What happens with focus management?

### Required Output
```markdown
## Pass 7: Accessibility

**Keyboard Navigation:**
| Action | Key | From | To |
|--------|-----|------|-----|
| [Action] | [Key] | [Element] | [Element] |

**Screen Reader Announcements:**
| Element | Announcement | When |
|---------|--------------|------|
| [Element] | [What's read] | [Trigger] |

**ARIA Requirements:**
| Element | Role | Properties |
|---------|------|------------|
| [Element] | [role] | [aria-*] |

**Focus Management:**
- Focus trap: [Where needed]
- Focus restoration: [After what actions]
- Skip links: [If needed]

**Touch Targets:**
- Minimum: 44x44px
- Elements needing adjustment: [List]
```

### Why This Pass Matters
15% of users have some form of disability. Accessibility is not optional.

---

## Pass 8: Responsive Strategy

**Designer mindset:** "How does this adapt to different screens?"

### Questions to Force
- Mobile-first or desktop-first?
- What content gets priority on small screens?
- How does navigation change?
- What about touch vs mouse?

### Required Output
```markdown
## Pass 8: Responsive Strategy

**Approach:** [Mobile-first / Desktop-first]

**Breakpoints:**
| Name | Width | Major Changes |
|------|-------|---------------|
| Mobile | <640px | [Changes] |
| Tablet | 640-1024px | [Changes] |
| Desktop | >1024px | [Changes] |

**Content Priority (Mobile):**
1. [Most important]
2. [Second]
3. [Hidden on mobile]

**Navigation Changes:**
| Viewport | Pattern | Reason |
|----------|---------|--------|
| Mobile | [Hamburger/Tabs] | [Why] |
| Desktop | [Sidebar/Top] | [Why] |

**Touch Considerations:**
- Swipe gestures: [Where]
- Long press: [Where]
- Hover alternatives: [For touch]
```

### Why This Pass Matters
Over 50% of web traffic is mobile. Design must work everywhere.

---

## Pass 9: Motion & Delight

**Designer mindset:** "How does this come alive?"

### Questions to Force
- What animations are meaningful (not decorative)?
- What feedback do micro-interactions provide?
- What about reduced motion preferences?
- What's the performance budget?

### Required Output
```markdown
## Pass 9: Motion & Delight

**Entry Animations:**
| Element | Animation | Duration | Easing | Purpose |
|---------|-----------|----------|--------|---------|
| [Element] | [Type] | [ms] | [curve] | [Why] |

**Exit Animations:**
| Element | Animation | Duration | Easing |
|---------|-----------|----------|--------|
| [Element] | [Type] | [ms] | [curve] |

**Micro-interactions:**
| Trigger | Response | Duration | Purpose |
|---------|----------|----------|---------|
| Button hover | [Effect] | [ms] | [Feedback type] |
| Success | [Effect] | [ms] | [Confirmation] |

**Reduced Motion:**
- All durations → 0ms or instant
- Transforms → static states
- Loops → single frame

**Performance Budget:**
- Max concurrent animations: 3
- Max total duration: 500ms
- Only animate: transform, opacity
```

### Why This Pass Matters
Motion provides feedback, guides attention, and creates delight. But it must be purposeful.

---

## Red Flags - Stop and Restart

If you catch yourself doing any of these, STOP and return to the passes:

| Violation | What You're Skipping |
|-----------|---------------------|
| Describing colors/fonts | All foundational passes |
| "The main screen shows..." | Pass 1-2 (mental model, IA) |
| Designing components before actions mapped | Pass 3 (affordances) |
| No friction point analysis | Pass 4 (cognitive load) |
| States only in component specs | Pass 5 (holistic state design) |
| No "where could they fail?" | Pass 6 (flow integrity) |
| No keyboard/screen reader plan | Pass 7 (accessibility) |
| Single viewport design only | Pass 8 (responsive strategy) |
| No animation/interaction plan | Pass 9 (motion & delight) |
| "User is in a hurry" | ALL passes |
| "Just this once, skip to visuals" | ALL passes |
| "The PRD is simple enough" | ALL passes |

---

## Common Mistakes

**Merging passes:** "I'll cover mental model while doing IA"
→ You won't. Separate passes force separate thinking.

**Skipping to visuals:** "The PRD is clear, I can design screens"
→ Screens without foundations need rework.

**Implicit affordances:** "Buttons are obviously clickable"
→ Map EVERY action explicitly. What's obvious to you isn't to users.

**Scattered state design:** "I'll add states to each component"
→ Holistic state table in Pass 5 catches gaps.

---

## Output Template

The final UX-spec.md should follow this structure:

```markdown
# UX Specification: [Product Name]

## Pass 1: Mental Model
[Required content]

## Pass 2: Information Architecture
[Required content]

## Pass 3: Affordances
[Required content]

## Pass 4: Cognitive Load
[Required content]

## Pass 5: State Design
[Required content]

## Pass 6: Flow Integrity
[Required content]

## Pass 7: Accessibility
[Required content]

## Pass 8: Responsive Strategy
[Required content]

## Pass 9: Motion & Delight
[Required content]

---

## Ready for Visual Phase
[Summary of constraints and rules for /frontend-design]
```
