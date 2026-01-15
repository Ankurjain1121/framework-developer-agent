# Component Patterns Library

Best-practice patterns for common UI components.

---

## How to Use

1. Identify the component category needed
2. Select the appropriate pattern
3. Adapt to project's design system
4. Include all states and accessibility

---

## Forms

### Single Field Form
```
Structure:
├── Label
├── Input
├── Helper text (optional)
└── Error message (on error)

States: default, focus, error, disabled, success
Accessibility: label linked to input, error announced
```

### Multi-Step Wizard
```
Structure:
├── Progress indicator
├── Step title
├── Form fields
├── Navigation (Back / Next)
└── Step indicator ("Step 2 of 4")

Patterns:
- Linear: must complete in order
- Non-linear: can jump to completed steps
- Save progress between steps
```

### Inline Editing
```
Structure:
├── Display mode (text + edit icon)
└── Edit mode (input + save/cancel)

Activation: click or icon click
Confirmation: save button or Enter
Cancellation: cancel button or Escape
```

### Search with Autocomplete
```
Structure:
├── Search input
├── Clear button (when has value)
├── Dropdown results
│   ├── Loading state
│   ├── Results list
│   └── No results
└── Recent searches (optional)

Keyboard: arrows to navigate, Enter to select, Escape to close
```

---

## Data Display

### Data Table
```
Structure:
├── Table header
│   ├── Column headers (sortable)
│   └── Bulk actions
├── Table body
│   └── Rows
│       ├── Selection checkbox
│       ├── Data cells
│       └── Row actions
├── Empty state
└── Pagination

Features:
- Sortable columns (single/multi)
- Filterable
- Selectable rows
- Resizable columns (optional)
- Sticky header (optional)
```

### Card Grid
```
Structure:
├── Grid container
│   └── Cards
│       ├── Image (optional)
│       ├── Title
│       ├── Description
│       ├── Metadata
│       └── Actions
└── Empty state

Layout: responsive grid (1/2/3/4 columns)
Loading: skeleton cards
```

### Empty State
```
Structure:
├── Illustration (optional)
├── Title
├── Description
└── Primary action

Variants:
- First use: explain what goes here
- No results: explain why empty, suggest actions
- Error: explain what went wrong, how to fix
```

---

## Navigation

### Tabs
```
Structure:
├── Tab list (role="tablist")
│   └── Tab buttons (role="tab")
└── Tab panels (role="tabpanel")

Keyboard: arrows between tabs, Tab to panel
States: active, hover, focus, disabled
```

### Breadcrumbs
```
Structure:
├── Home link
├── Separator
├── Parent links...
├── Separator
└── Current page (not linked)

Accessibility: nav with aria-label="Breadcrumb"
Truncation: collapse middle items with "..."
```

### Pagination
```
Structure:
├── Previous button
├── Page numbers
│   ├── First page
│   ├── ... (ellipsis)
│   ├── Nearby pages
│   ├── ... (ellipsis)
│   └── Last page
└── Next button

Variants:
- Numbered: 1, 2, 3...
- Load more: single button
- Infinite scroll: auto-load
```

### Sidebar Navigation
```
Structure:
├── Logo/branding
├── Primary nav
│   └── Nav items
│       ├── Icon
│       ├── Label
│       └── Badge (optional)
├── Secondary nav
└── User section

States: expanded, collapsed (icons only)
Mobile: drawer overlay
```

---

## Feedback

### Toast/Notification
```
Structure:
├── Icon (success/error/warning/info)
├── Message
├── Action (optional)
└── Dismiss button

Position: top-right or bottom-right
Duration: 3-5 seconds (or persistent for errors)
Stacking: newest on top, max 3 visible
```

### Progress Indicator
```
Variants:
- Determinate: known progress (% bar)
- Indeterminate: unknown duration (spinner)
- Steps: multi-stage (1 of 3)

Structure (determinate):
├── Progress bar
├── Percentage text
└── Status message
```

### Skeleton Loader
```
Structure:
├── Skeleton shapes matching content
│   ├── Text lines (varying widths)
│   ├── Image placeholder
│   └── Avatar circle
└── Shimmer animation

Rules:
- Match actual content layout
- No jarring layout shift when loaded
- Animate subtly
```

---

## Overlays

### Modal/Dialog
```
Structure:
├── Backdrop (click to close optional)
├── Dialog container
│   ├── Header
│   │   ├── Title
│   │   └── Close button
│   ├── Content
│   └── Footer (actions)

Focus: trap inside, return on close
Close: X button, Escape, backdrop click
Accessibility: role="dialog", aria-modal="true"
```

### Drawer/Sheet
```
Structure:
├── Backdrop
├── Drawer panel
│   ├── Header
│   ├── Content
│   └── Footer (optional)

Position: left, right, bottom
Mobile: full-width from bottom
Animation: slide in from edge
```

### Popover
```
Structure:
├── Trigger element
└── Popover content
    ├── Arrow (optional)
    └── Content

Position: auto-flip to stay in viewport
Close: click outside, Escape
Focus: manage with floating-ui
```

### Tooltip
```
Structure:
├── Trigger element
└── Tooltip
    └── Text content

Trigger: hover (desktop), focus, long-press (mobile)
Delay: 200-300ms before show
Position: auto-flip
```

---

## Component Checklist

For every component, ensure:

- [ ] All states defined (default, hover, focus, active, disabled, error)
- [ ] Loading state
- [ ] Empty state
- [ ] Error state
- [ ] Keyboard accessible
- [ ] Screen reader friendly
- [ ] Responsive behavior
- [ ] Reduced motion support
