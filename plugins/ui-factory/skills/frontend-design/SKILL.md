---
name: frontend-design
description: Create distinctive, production-grade frontend interfaces with high design quality
color: cyan
tools: [Read, Write, Edit, Glob, Grep, Task]
---

# /frontend-design

## Overview

Transform UX specifications into production-ready React components using shadcn/ui. This skill focuses on creating distinctive, polished interfaces that avoid generic AI aesthetics.

## Subcommands

### `/frontend-design component <name>`
Generate a single component from spec or description.

### `/frontend-design page <name>`
Generate a full page layout with multiple components.

### `/frontend-design form <name>`
Generate a form with validation, states, and accessibility.

---

## Process

### Phase 1: Spec Analysis

```
1. READ the UX specification (if exists)
   - Check for: specs/[feature]-ux.md
   - Extract: user flows, component requirements, interactions

2. IDENTIFY component hierarchy
   - Page layout structure
   - Component boundaries
   - Shared vs unique components

3. MAP to shadcn/ui primitives
   - Reference: references/component-mapping.md
   - Note required customizations
```

### Phase 2: Component Architecture

```
For each component:
1. Define TypeScript interface
2. Identify shadcn/ui base components
3. Plan composition structure
4. List required states (loading, error, empty, success)
5. Note accessibility requirements
```

### Phase 3: Code Generation

```
1. Generate component files following templates
2. Include all state variations
3. Add proper TypeScript types
4. Implement accessibility (ARIA, keyboard nav)
5. Apply aesthetic guidelines
```

### Phase 4: Integration

```
1. Export from feature index
2. Create/update page composition
3. Wire up data fetching hooks
4. Test all states manually
```

---

## Integration with /orchestrate

When building multiple components:

```markdown
## Parallel Build Strategy

For 5+ independent components, spawn parallel agents:

| Agent | Assignment | Contract |
|-------|------------|----------|
| Agent 1 | Header, Navigation | exports: HeaderProps, NavItem[] |
| Agent 2 | DataTable, Pagination | exports: Column[], PaginationProps |
| Agent 3 | Forms, Inputs | exports: FormSchema, FieldProps |
| Agent 4 | Modals, Dialogs | exports: ModalProps, DialogConfig |
| Agent 5 | Cards, Lists | exports: CardProps, ListItemProps |

Each agent receives:
- Component spec section
- Shared types/contracts
- Aesthetic guidelines (REQUIRED)
- Anti-slop rules (REQUIRED)
```

---

## Anti-AI-Slop Rules (MANDATORY)

These rules MUST be followed in ALL generated code:

### Typography
```
FORBIDDEN:
- Using Inter as the ONLY font
- Generic font-sans without customization
- All text same weight (everything medium/regular)
- No typographic hierarchy

REQUIRED:
- Define font pairing (heading + body)
- Use weight variation (400, 500, 600, 700)
- Establish clear size scale
- Consider system fonts for performance
```

### Colors
```
FORBIDDEN:
- Purple/blue gradients everywhere
- Neon accent colors
- Low contrast text
- Rainbow hover states
- Generic blue (#3B82F6) as only accent

REQUIRED:
- Intentional color palette (max 5 colors)
- Sufficient contrast (WCAG AA minimum)
- Muted, sophisticated tones
- Consistent accent usage
```

### Borders & Corners
```
FORBIDDEN:
- rounded-full on everything
- rounded-3xl cards
- Excessive border-radius variety
- No borders (floating elements)

REQUIRED:
- Consistent radius scale (rounded-sm, rounded-md, rounded-lg)
- Subtle borders for definition
- Sharp corners where appropriate (data tables, code blocks)
- Max rounded-xl for cards
```

### Animation
```
FORBIDDEN:
- Everything bouncing
- Slow transitions (>300ms)
- Animation for animation's sake
- Parallax scrolling
- Floating elements

REQUIRED:
- Purposeful micro-interactions
- Fast, subtle transitions (150-200ms)
- Reduce motion support
- Only animate state changes
```

### Layout
```
FORBIDDEN:
- Excessive whitespace
- Everything centered
- No visual hierarchy
- Generic card grids
- Hero sections with stock photos

REQUIRED:
- Intentional spacing (8px grid)
- Clear content hierarchy
- Purposeful alignment
- Dense where appropriate (dashboards)
```

### Content
```
FORBIDDEN:
- "Lorem ipsum" in demos
- Generic placeholder images
- "Click here" buttons
- Emoji overuse
- Marketing speak in UI

REQUIRED:
- Realistic placeholder content
- Descriptive button labels
- Clear, concise microcopy
- Professional tone
```

---

## Component Generation Template

```tsx
// FILE: components/[feature]/[component-name].tsx

import { type ComponentProps } from 'react'
import { cn } from '@/lib/utils'
// Import specific shadcn components
import { Button } from '@/components/ui/button'

// Types first
interface [ComponentName]Props {
  // Required props
  data: DataType
  // Optional props with defaults
  variant?: 'default' | 'compact'
  // Event handlers
  onAction?: (id: string) => void
  // Styling
  className?: string
}

// Component
export function [ComponentName]({
  data,
  variant = 'default',
  onAction,
  className,
}: [ComponentName]Props) {
  // Loading state
  if (!data) {
    return <[ComponentName]Skeleton />
  }

  // Empty state
  if (data.length === 0) {
    return <[ComponentName]Empty />
  }

  // Main render
  return (
    <div className={cn('[base-styles]', className)}>
      {/* Content */}
    </div>
  )
}

// Skeleton component
function [ComponentName]Skeleton() {
  return (
    <div className="animate-pulse">
      {/* Skeleton structure */}
    </div>
  )
}

// Empty state component
function [ComponentName]Empty() {
  return (
    <div className="text-center py-12">
      <p className="text-muted-foreground">No items found</p>
    </div>
  )
}
```

---

## Form Generation Template

```tsx
// FILE: components/[feature]/[form-name]-form.tsx

import { useForm } from 'react-hook-form'
import { zodResolver } from '@hookform/resolvers/zod'
import { z } from 'zod'
import {
  Form,
  FormControl,
  FormDescription,
  FormField,
  FormItem,
  FormLabel,
  FormMessage,
} from '@/components/ui/form'
import { Input } from '@/components/ui/input'
import { Button } from '@/components/ui/button'

// Schema
const formSchema = z.object({
  fieldName: z.string().min(1, 'Required'),
})

type FormValues = z.infer<typeof formSchema>

// Props
interface [FormName]FormProps {
  defaultValues?: Partial<FormValues>
  onSubmit: (values: FormValues) => Promise<void>
  onCancel?: () => void
}

export function [FormName]Form({
  defaultValues,
  onSubmit,
  onCancel,
}: [FormName]FormProps) {
  const form = useForm<FormValues>({
    resolver: zodResolver(formSchema),
    defaultValues: {
      fieldName: '',
      ...defaultValues,
    },
  })

  const handleSubmit = async (values: FormValues) => {
    try {
      await onSubmit(values)
      form.reset()
    } catch (error) {
      // Error handled by parent
    }
  }

  return (
    <Form {...form}>
      <form onSubmit={form.handleSubmit(handleSubmit)} className="space-y-4">
        <FormField
          control={form.control}
          name="fieldName"
          render={({ field }) => (
            <FormItem>
              <FormLabel>Field Label</FormLabel>
              <FormControl>
                <Input placeholder="Enter value..." {...field} />
              </FormControl>
              <FormDescription>Help text here.</FormDescription>
              <FormMessage />
            </FormItem>
          )}
        />

        <div className="flex justify-end gap-2">
          {onCancel && (
            <Button type="button" variant="outline" onClick={onCancel}>
              Cancel
            </Button>
          )}
          <Button type="submit" disabled={form.formState.isSubmitting}>
            {form.formState.isSubmitting ? 'Saving...' : 'Save'}
          </Button>
        </div>
      </form>
    </Form>
  )
}
```

---

## Page Layout Template

```tsx
// FILE: app/(dashboard)/[feature]/page.tsx

import { Suspense } from 'react'
import { [Feature]Header } from '@/components/[feature]/[feature]-header'
import { [Feature]Content } from '@/components/[feature]/[feature]-content'
import { [Feature]Skeleton } from '@/components/[feature]/[feature]-skeleton'

export default function [Feature]Page() {
  return (
    <div className="flex flex-col gap-6">
      <[Feature]Header />

      <Suspense fallback={<[Feature]Skeleton />}>
        <[Feature]Content />
      </Suspense>
    </div>
  )
}
```

---

## State Patterns

### Loading States
```tsx
// Skeleton - for initial load
<div className="animate-pulse bg-muted rounded h-4 w-full" />

// Spinner - for actions
<Loader2 className="h-4 w-4 animate-spin" />

// Progress - for uploads/long operations
<Progress value={progress} />
```

### Error States
```tsx
// Inline error
<Alert variant="destructive">
  <AlertCircle className="h-4 w-4" />
  <AlertDescription>{error.message}</AlertDescription>
</Alert>

// Full page error
<div className="flex flex-col items-center justify-center min-h-[400px]">
  <AlertCircle className="h-12 w-12 text-destructive mb-4" />
  <h2 className="text-lg font-semibold">Something went wrong</h2>
  <p className="text-muted-foreground">{error.message}</p>
  <Button onClick={retry} className="mt-4">Try again</Button>
</div>
```

### Empty States
```tsx
// With action
<div className="text-center py-12">
  <FileQuestion className="h-12 w-12 text-muted-foreground mx-auto mb-4" />
  <h3 className="font-medium">No projects yet</h3>
  <p className="text-sm text-muted-foreground mt-1">
    Create your first project to get started.
  </p>
  <Button className="mt-4">Create Project</Button>
</div>

// Without action
<div className="text-center py-12">
  <Search className="h-12 w-12 text-muted-foreground mx-auto mb-4" />
  <h3 className="font-medium">No results found</h3>
  <p className="text-sm text-muted-foreground mt-1">
    Try adjusting your search or filters.
  </p>
</div>
```

---

## Accessibility Checklist

```
[ ] All interactive elements are keyboard accessible
[ ] Focus states are visible
[ ] ARIA labels on icon-only buttons
[ ] Form fields have associated labels
[ ] Error messages are announced to screen readers
[ ] Color is not the only indicator of state
[ ] Sufficient color contrast (4.5:1 for text)
[ ] Skip links for navigation
[ ] Heading hierarchy (h1 → h2 → h3)
[ ] Alt text for meaningful images
```

---

## File Organization

```
components/
└── [feature]/
    ├── index.ts              # Exports
    ├── [feature]-header.tsx  # Page header
    ├── [feature]-list.tsx    # List/table view
    ├── [feature]-card.tsx    # Card component
    ├── [feature]-form.tsx    # Create/edit form
    ├── [feature]-dialog.tsx  # Modal dialogs
    └── [feature]-skeleton.tsx # Loading states
```

---

## Quality Checklist

Before completing:

```
[ ] All components have TypeScript types
[ ] All states handled (loading, error, empty, success)
[ ] Anti-AI-slop rules followed
[ ] Accessibility requirements met
[ ] Consistent spacing (8px grid)
[ ] No console.log statements
[ ] No commented code
[ ] No unused imports
[ ] Responsive design considered
[ ] Dark mode compatible (if applicable)
```

---

## References

- [Component Mapping](references/component-mapping.md) - UX patterns to shadcn/ui
- [Code Templates](references/code-templates.md) - Reusable code patterns
- [Aesthetic Guidelines](references/aesthetic-guidelines.md) - Design rules
