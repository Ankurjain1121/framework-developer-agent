# UI Improve

Improve existing UI components by fixing audit issues automatically.

## Command

```
/ui-factory:ui-improve
```

## Subcommands

| Subcommand | Description |
|------------|-------------|
| `<path>` | Improve specific component file or directory |
| `--all` | Improve all components in src/components |

## Usage Examples

```bash
/ui-factory:ui-improve src/components/dashboard/stats-card.tsx
/ui-factory:ui-improve src/components/forms/
/ui-factory:ui-improve --all
```

---

## Process

### Phase 1: Audit (Parallel)

First, run `/ui-factory:ui-audit` to identify all issues.

```
Spawn parallel auditors:
├── style-auditor → Typography, colors, spacing issues
└── a11y-auditor → Accessibility violations
```

Collect and categorize all issues before fixing.

### Phase 2: Spawn Fixers (Parallel via /orchestrate)

Based on audit results, spawn specialized fixers using `/orchestrate`.

**Fixer Agents:**

| Agent | Responsibility | File Ownership |
|-------|----------------|----------------|
| `typography-fixer` | Font sizes, weights, line heights, font families | Classes: `text-*`, `font-*`, `leading-*`, `tracking-*` |
| `spacing-fixer` | Margins, paddings, gaps, layout spacing | Classes: `m-*`, `p-*`, `gap-*`, `space-*` |
| `states-fixer` | Hover, focus, active, disabled states | Pseudo-classes: `hover:`, `focus:`, `active:`, `disabled:` |
| `motion-fixer` | Transitions, animations, transforms | Classes: `transition-*`, `animate-*`, `transform`, `duration-*` |
| `a11y-fixer` | ARIA labels, roles, keyboard navigation | Attributes: `aria-*`, `role`, `tabIndex`, `onKeyDown` |

### Phase 3: Apply Fixes

Each fixer operates on its designated classes/attributes only.

**File Ownership Rules (NO OVERLAP):**

```
typography-fixer OWNS:
  - text-xs, text-sm, text-base, text-lg, text-xl, text-2xl...
  - font-thin, font-light, font-normal, font-medium, font-semibold, font-bold
  - leading-none, leading-tight, leading-snug, leading-normal, leading-relaxed
  - tracking-tighter, tracking-tight, tracking-normal, tracking-wide

spacing-fixer OWNS:
  - m-0, m-1, m-2... mx-*, my-*, mt-*, mr-*, mb-*, ml-*
  - p-0, p-1, p-2... px-*, py-*, pt-*, pr-*, pb-*, pl-*
  - gap-*, space-x-*, space-y-*

states-fixer OWNS:
  - hover:* (all hover states)
  - focus:* (all focus states)
  - focus-visible:* (keyboard focus)
  - active:* (all active states)
  - disabled:* (all disabled states)

motion-fixer OWNS:
  - transition, transition-all, transition-colors, transition-opacity...
  - duration-75, duration-100, duration-150, duration-200...
  - ease-linear, ease-in, ease-out, ease-in-out
  - animate-spin, animate-ping, animate-pulse, animate-bounce
  - transform, scale-*, rotate-*, translate-*

a11y-fixer OWNS:
  - aria-label, aria-labelledby, aria-describedby
  - aria-hidden, aria-expanded, aria-selected, aria-checked
  - role attribute
  - tabIndex attribute
  - onKeyDown, onKeyUp handlers for keyboard nav
```

### Phase 4: Show Before/After Diff

After fixes are applied, display a summary diff:

```diff
// Example output format

--- Before: src/components/button.tsx
+++ After: src/components/button.tsx

- className="text-sm p-2 bg-blue-500"
+ className="text-sm p-2.5 bg-blue-500 hover:bg-blue-600 focus:ring-2 focus:ring-blue-500 focus:ring-offset-2 transition-colors duration-150"

Issues Fixed: 3
├── [states] Added hover state
├── [states] Added focus ring
└── [motion] Added transition
```

### Phase 5: Re-Audit

Run `/ui-factory:ui-audit` again to verify fixes and catch any remaining issues.

**Expected output:**

```
Re-Audit Results
================

Before: 12 issues (3 Critical, 5 Major, 4 Minor)
After:  2 issues (0 Critical, 1 Major, 1 Minor)

Remaining Issues:
| Severity | Type | File | Issue |
|----------|------|------|-------|
| Major | a11y | button.tsx | Missing aria-label on icon-only button |
| Minor | style | card.tsx | Inconsistent border radius |

✓ 10 issues fixed automatically
! 2 issues require manual review
```

---

## Orchestration Template

When spawning fixers, use this `/orchestrate` configuration:

```yaml
agents:
  typography-fixer:
    files:
      - <target-files>
    task: |
      Fix typography issues from audit:
      <typography-issues>

      ONLY modify: text-*, font-*, leading-*, tracking-* classes
      DO NOT touch: spacing, states, motion, or a11y

  spacing-fixer:
    files:
      - <target-files>
    task: |
      Fix spacing issues from audit:
      <spacing-issues>

      ONLY modify: m-*, p-*, gap-*, space-* classes
      DO NOT touch: typography, states, motion, or a11y

  states-fixer:
    files:
      - <target-files>
    task: |
      Fix interaction state issues from audit:
      <states-issues>

      ONLY modify: hover:*, focus:*, active:*, disabled:* classes
      DO NOT touch: typography, spacing, motion, or a11y

  motion-fixer:
    files:
      - <target-files>
    task: |
      Fix motion/animation issues from audit:
      <motion-issues>

      ONLY modify: transition-*, duration-*, animate-*, transform classes
      DO NOT touch: typography, spacing, states, or a11y

  a11y-fixer:
    files:
      - <target-files>
    task: |
      Fix accessibility issues from audit:
      <a11y-issues>

      ONLY modify: aria-*, role, tabIndex, keyboard handlers
      DO NOT touch: styling classes
```

---

## Conflict Prevention

**Rule: One class category = One fixer**

If a line needs changes from multiple fixers:
1. Typography fixer runs first (base text)
2. Spacing fixer runs second (layout)
3. States fixer runs third (interactions)
4. Motion fixer runs fourth (animations)
5. A11y fixer runs last (accessibility attributes)

**Merge Strategy:**
- Each fixer appends its classes, never removes others' classes
- A11y fixer adds attributes, doesn't modify className

---

## Error Handling

```
If audit returns 0 issues:
  → "✓ No issues found. Component is clean."

If fixer fails:
  → Report which fixer failed
  → Continue with other fixers
  → Mark failed fixes as "requires manual review"

If re-audit finds new issues:
  → Flag as regression
  → Rollback that fixer's changes
  → Report for manual review
```

---

## Integration

**Depends on:**
- `/ui-factory:ui-audit` - Must run first to identify issues
- `/orchestrate` - Spawns parallel fixers

**Outputs:**
- Fixed component files
- Before/after diff report
- Re-audit results
- List of issues requiring manual review
