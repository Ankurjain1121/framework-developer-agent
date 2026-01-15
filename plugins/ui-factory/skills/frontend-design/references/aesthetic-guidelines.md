# Aesthetic Guidelines

Rules for creating distinctive, polished interfaces that avoid generic AI aesthetics.

---

## Anti-AI-Slop Rules

### The Problem
AI-generated UIs often share recognizable patterns that make them look generic:
- Purple/blue gradient backgrounds
- Excessive rounded corners on everything
- Over-animated elements
- Generic Inter font with no hierarchy
- Stock photo heroes with overlay gradients
- Neon accent colors

### The Solution
Follow these rules to create distinctive interfaces.

---

## Typography Rules

### Font Selection
```
FORBIDDEN:
- Inter as the ONLY font
- Generic system font-sans with no customization
- All text at the same weight
- Decorative fonts for body text

APPROVED:
- System font stack for performance
- Intentional font pairing (heading + body)
- Weight variation to create hierarchy
- Monospace for code/data
```

### Hierarchy Scale
```tsx
// Headings - use tracking-tight for large text
<h1 className="text-3xl font-bold tracking-tight">Page Title</h1>
<h2 className="text-2xl font-semibold tracking-tight">Section Title</h2>
<h3 className="text-lg font-semibold">Subsection</h3>
<h4 className="text-base font-medium">Card Title</h4>

// Body text
<p className="text-base">Default body text</p>
<p className="text-sm text-muted-foreground">Secondary text</p>
<p className="text-xs text-muted-foreground">Caption/metadata</p>
```

### Weight Usage
```
400 (regular): Body text, descriptions
500 (medium): Labels, secondary headings, buttons
600 (semibold): Primary headings, emphasis
700 (bold): Page titles, strong emphasis (use sparingly)
```

---

## Color Rules

### Palette Constraints
```
FORBIDDEN:
- Purple gradients as primary
- Neon/saturated accents (#00FF00, #FF00FF)
- Rainbow hover states
- Low contrast text (<4.5:1)
- More than 5 colors in palette

APPROVED:
- Muted, sophisticated tones
- High contrast (WCAG AA minimum)
- Consistent accent usage
- Monochromatic with single accent
```

### Color Usage Patterns
```tsx
// Primary actions
<Button>Submit</Button> // Uses primary color

// Secondary/outline for less important actions
<Button variant="outline">Cancel</Button>

// Destructive for danger
<Button variant="destructive">Delete</Button>

// Ghost for tertiary actions
<Button variant="ghost">More options</Button>

// Muted foreground for secondary text
<p className="text-muted-foreground">Help text</p>
```

### Status Colors
```tsx
// Use semantic colors for status
const statusColors = {
  success: 'text-green-600 bg-green-50',
  warning: 'text-yellow-600 bg-yellow-50',
  error: 'text-red-600 bg-red-50',
  info: 'text-blue-600 bg-blue-50',
}

// Badges
<Badge variant="default">Active</Badge>
<Badge variant="secondary">Pending</Badge>
<Badge variant="destructive">Failed</Badge>
<Badge variant="outline">Draft</Badge>
```

---

## Border & Corner Rules

### Radius Scale
```
FORBIDDEN:
- rounded-full on cards
- rounded-3xl anything
- Inconsistent radius within same component
- No borders (floating elements)

APPROVED SCALE:
- rounded-sm (2px): Subtle rounding, inputs
- rounded (4px): Default, most elements
- rounded-md (6px): Cards, dialogs
- rounded-lg (8px): Large cards, modals
- rounded-xl (12px): Maximum for containers
- rounded-full: Only for avatars, icons, pills
```

### Application
```tsx
// Inputs - subtle rounding
<Input className="rounded-sm" />

// Cards - moderate rounding
<Card className="rounded-lg" />

// Buttons - match inputs
<Button className="rounded-sm" />

// Avatars - full rounding
<Avatar className="rounded-full" />

// Data tables - sharp or subtle
<Table className="rounded-none" />  // Sharp for data density
```

### Border Usage
```tsx
// Subtle borders for definition
<div className="border border-border" />

// Stronger borders for focus
<div className="border-2 border-primary" />

// Use ring for focus states
<input className="focus-visible:ring-2 focus-visible:ring-ring" />
```

---

## Animation Rules

### Timing
```
FORBIDDEN:
- Transitions > 300ms
- Bouncing/elastic easing on everything
- Animation for decorative purposes
- Parallax scrolling
- Auto-playing animations

APPROVED:
- 150ms: Micro-interactions (hover, focus)
- 200ms: Standard transitions
- 300ms: Larger movements (modals, sheets)
```

### Implementation
```tsx
// Hover states - fast
<button className="transition-colors duration-150 hover:bg-accent" />

// Focus states - instant
<input className="transition-shadow focus:ring-2" />

// Modals/sheets - moderate
<Dialog className="data-[state=open]:animate-in data-[state=closed]:animate-out duration-200" />

// Loading spinner - continuous but subtle
<Loader2 className="h-4 w-4 animate-spin" />
```

### Reduce Motion
```tsx
// Always respect user preference
<div className="motion-reduce:transition-none motion-reduce:animate-none">
  {/* Content */}
</div>
```

---

## Spacing Rules

### 8px Grid System
```
Base unit: 8px (0.5rem)

Scale:
- 4px (0.25rem, p-1): Tight spacing, icon padding
- 8px (0.5rem, p-2): Small gaps
- 12px (0.75rem, p-3): Default input padding
- 16px (1rem, p-4): Standard spacing
- 24px (1.5rem, p-6): Section spacing
- 32px (2rem, p-8): Large gaps
- 48px (3rem, p-12): Page sections
```

### Application
```tsx
// Component internal spacing
<Card>
  <CardHeader className="p-4">    {/* 16px */}
  <CardContent className="p-4 pt-0">
  <CardFooter className="p-4 pt-0">
</Card>

// Gap between items
<div className="flex gap-2">      {/* 8px */}
<div className="flex gap-4">      {/* 16px */}
<div className="space-y-4">       {/* 16px vertical */}

// Page sections
<section className="py-12">       {/* 48px vertical */}
```

### Consistency
```
FORBIDDEN:
- Mixing arbitrary spacing values
- Different spacing for same component type
- Excessive whitespace (py-24 everywhere)

REQUIRED:
- Use Tailwind spacing scale
- Consistent padding within components
- Dense spacing for data-heavy UIs
```

---

## Layout Rules

### Content Width
```tsx
// Max width for readability
<div className="max-w-prose">     {/* 65ch - for text */}
<div className="max-w-2xl">       {/* 672px - forms */}
<div className="max-w-4xl">       {/* 896px - content */}
<div className="max-w-6xl">       {/* 1152px - dashboards */}
<div className="max-w-7xl">       {/* 1280px - full layouts */}
```

### Alignment
```
FORBIDDEN:
- Everything centered
- Inconsistent alignment within sections
- Left-align some cards, center others

REQUIRED:
- Left-align body text
- Left-align form labels
- Center empty states
- Right-align numeric data in tables
```

### Visual Hierarchy
```tsx
// Primary content - prominent
<main className="flex-1">
  <header className="pb-4 border-b">
    {/* Page title, primary action */}
  </header>
  <div className="py-6">
    {/* Main content */}
  </div>
</main>

// Secondary content - subdued
<aside className="w-64 border-l bg-muted/50">
  {/* Sidebar, filters */}
</aside>
```

---

## Content Rules

### Microcopy
```
FORBIDDEN:
- "Click here"
- "Submit" (be specific)
- Lorem ipsum in production
- Emoji overuse
- Marketing speak ("Amazing!", "Revolutionary!")

APPROVED:
- "Save changes"
- "Create project"
- "Download report"
- Realistic placeholder content
- Clear, concise labels
```

### Button Labels
```tsx
// Be specific about the action
<Button>Create Project</Button>    // Not "Submit"
<Button>Save Changes</Button>      // Not "Save"
<Button>Send Invitation</Button>   // Not "Send"
<Button>Download PDF</Button>      // Not "Download"
```

### Empty States
```tsx
// Descriptive and helpful
<EmptyState
  title="No projects yet"
  description="Create your first project to start tracking tasks and collaborating with your team."
  action={{ label: "Create Project", onClick: handleCreate }}
/>

// Not generic
// BAD: "No data", "Empty", "Nothing here"
```

---

## Component Checklist

Before finalizing any component:

```
[ ] Typography hierarchy is clear
[ ] Colors are from approved palette
[ ] Contrast meets WCAG AA (4.5:1 text, 3:1 UI)
[ ] Border radius is consistent
[ ] Animations are < 300ms
[ ] Spacing follows 8px grid
[ ] Content is realistic (no lorem ipsum)
[ ] Button labels are specific
[ ] Empty/loading/error states exist
[ ] Reduced motion is respected
[ ] No AI-slop patterns present
```
