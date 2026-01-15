# UI Audit

Audit UI components for style, accessibility, and UX issues without making changes.

## Command

```
/ui-factory:ui-audit
```

## Subcommands

| Subcommand | Description |
|------------|-------------|
| `<path>` | Audit specific component file or directory |
| `--all` | Audit all components in src/components |

## Usage Examples

```bash
/ui-factory:ui-audit src/components/dashboard/stats-card.tsx
/ui-factory:ui-audit src/components/forms/
/ui-factory:ui-audit --all
```

---

## Process

### Phase 1: Spawn Parallel Auditors

```
Spawn in parallel:
├── style-auditor
│   ├── Typography (font sizes, weights, line heights)
│   ├── Colors (contrast, consistency, dark mode)
│   ├── Spacing (margins, paddings, gaps)
│   └── States (hover, focus, active, disabled)
│
└── a11y-auditor
    ├── ARIA labels and roles
    ├── Keyboard navigation
    ├── Focus indicators
    └── Screen reader compatibility
```

### Phase 2: Collect Issues

Each auditor returns issues in this format:

```typescript
interface AuditIssue {
  severity: 'critical' | 'major' | 'minor';
  type: 'typography' | 'spacing' | 'states' | 'motion' | 'a11y';
  file: string;
  line: number;
  issue: string;
  suggestion: string;
}
```

### Phase 3: Generate Prioritized Report

---

## Output Format

```
UI Audit Report
===============

Target: src/components/dashboard/
Files scanned: 12
Issues found: 8

| Severity | Type | File | Line | Issue |
|----------|------|------|------|-------|
| Critical | a11y | button.tsx | 24 | Missing aria-label on icon-only button |
| Critical | states | card.tsx | 45 | No focus indicator on interactive element |
| Major | typography | header.tsx | 12 | Font size too small (text-xs) for body text |
| Major | spacing | form.tsx | 67 | Inconsistent gap (gap-2 vs gap-4 in same context) |
| Major | a11y | modal.tsx | 33 | Focus not trapped inside modal |
| Minor | motion | button.tsx | 24 | Missing transition on hover state |
| Minor | states | link.tsx | 8 | No hover state on clickable element |
| Minor | spacing | card.tsx | 52 | Padding inconsistent with design system |

Summary
-------
Critical: 2
Major: 3
Minor: 3

Fix: /ui-factory:ui-improve src/components/dashboard/
```

---

## Severity Definitions

| Severity | Criteria | Examples |
|----------|----------|----------|
| **Critical** | Blocks functionality or accessibility | Missing labels, no keyboard access, no focus indicators |
| **Major** | Significant UX degradation | Poor contrast, inconsistent spacing, missing states |
| **Minor** | Polish and refinement | Missing transitions, minor inconsistencies |

---

## Auditor Agent Prompts

### style-auditor

```
Audit the following files for style issues:
<files>

Check for:
1. Typography
   - Font sizes appropriate for context (body >= text-sm)
   - Line heights readable (leading-normal or higher for body)
   - Font weights consistent with hierarchy

2. Spacing
   - Consistent use of spacing scale
   - Adequate padding for touch targets (min p-2)
   - Logical margin/gap patterns

3. States
   - All interactive elements have hover states
   - Focus indicators present and visible
   - Disabled states clearly different
   - Active/pressed states defined

4. Motion
   - Transitions on state changes
   - Appropriate durations (150-300ms)
   - No jarring instant changes

Return ONLY issues in JSON format:
[{ severity, type, file, line, issue, suggestion }]
```

### a11y-auditor

```
Audit the following files for accessibility:
<files>

Check for:
1. Labels
   - All interactive elements have accessible names
   - Icon-only buttons have aria-label
   - Form inputs have associated labels

2. Keyboard
   - All interactive elements focusable
   - Logical tab order
   - Keyboard handlers for custom widgets

3. ARIA
   - Correct roles on custom elements
   - Required ARIA attributes present
   - No misused ARIA

4. Focus Management
   - Focus visible on all elements
   - Focus trapped in modals/dialogs
   - Focus restored after modal close

Return ONLY issues in JSON format:
[{ severity, type, file, line, issue, suggestion }]
```

---

## Integration

**Used by:**
- `/ui-factory:ui-improve` - Runs audit first, then fixes

**Standalone:**
- Can be run independently to assess component quality
- Useful for code review and QA

**Next Step:**
After audit, suggest: `Fix: /ui-factory:ui-improve <path>`
