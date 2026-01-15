# UI Factory Plugin

Complete UI development pipeline that enforces design thinking before visual implementation.

## Skills Included

| Skill | Command | Purpose |
|-------|---------|---------|
| Design | `/design` | PRD → 9-pass UX spec pipeline |
| Frontend Design | `/frontend-design` | Create production-ready React components |
| UI Improve | `/ui-improve` | Enhance existing UI components |
| UI Audit | `/ui-audit` | Analyze UI for issues |
| Orchestrate | `/orchestrate` | Parallel agent execution |

## Pipeline Flow

```
Rough Idea → /design init → PRD
         → /design ux → UX Spec (9 passes)
         → /design build → /frontend-design → Components

Existing UI → /ui-audit → Issues Report
          → /ui-improve → Fixed Components
```

## The 9-Pass UX Methodology

1. **Mental Model** - What does the user think is happening?
2. **Information Architecture** - What exists, how is it organized?
3. **Affordances** - What actions are obvious without explanation?
4. **Cognitive Load** - Where will the user hesitate?
5. **State Design** - How does the system talk back?
6. **Flow Integrity** - Does this feel inevitable?
7. **Accessibility** - Can everyone use this?
8. **Responsive** - How does this adapt to screens?
9. **Motion** - How does this come alive?

## Anti-AI-Slop Rules

- No generic Inter-only typography
- No purple gradients everywhere
- No excessive rounded corners
- No animation for animation's sake
- No low-contrast text
- No everything-centered layouts

## Usage

```bash
# Full design pipeline
/design "user dashboard with analytics"

# Create components from spec
/frontend-design component data-table

# Audit existing UI
/ui-audit src/components/

# Improve with parallel agents
/ui-improve src/components/
```

## Requirements

- shadcn/ui components installed
- Tailwind CSS configured
- React 18+ with TypeScript
