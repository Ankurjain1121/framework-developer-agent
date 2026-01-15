# Design Token Extraction Guide

How to extract existing design tokens or create them from scratch.

---

## What Are Design Tokens?

Design tokens are the smallest pieces of a design system:
- Colors
- Typography
- Spacing
- Border radius
- Shadows
- Breakpoints

They enable consistency and make theming possible.

---

## Extraction Sources

| Source | What to Extract | Command/Location |
|--------|-----------------|------------------|
| tailwind.config.js | All theme values | Read `theme.extend` |
| CSS variables | :root definitions | Grep for `--` |
| Component styles | Repeated values | Pattern analysis |
| Figma exports | Design tokens | JSON export |

---

## Step 1: Detect Existing Tokens

### Check Tailwind Config
```bash
# Look for tailwind.config.js or tailwind.config.ts
# Extract theme.colors, theme.spacing, etc.
```

### Check Global CSS
```bash
# Look for :root { } blocks
# Extract all --variable definitions
```

### Check Package.json
```bash
# Look for design system packages:
# @radix-ui/colors, @chakra-ui/theme, etc.
```

---

## Step 2: Map Existing Tokens

Create a token inventory:

```markdown
## Current Tokens

### Colors (found in tailwind.config.js)
| Name | Value | Usage |
|------|-------|-------|
| primary | #3B82F6 | From theme.colors.primary |
| gray-100 | #F3F4F6 | From theme.colors.gray |

### Spacing (found in tailwind.config.js)
| Name | Value | Notes |
|------|-------|-------|
| 1 | 0.25rem | Default scale |
| 2 | 0.5rem | Default scale |

### Missing Tokens
- [ ] Semantic colors (success, error, warning)
- [ ] Shadow scale
- [ ] Border radius scale
```

---

## Step 3: Fill Gaps

If tokens are missing, generate them:

### Color Palette
```
Brand colors → Primary, Secondary
Semantic → Success (#10B981), Error (#EF4444), Warning (#F59E0B)
Neutral → Gray scale (50-900)
Surface → Background, Surface, Border
```

### Typography Scale
```
Based on 1rem (16px):
xs: 0.75rem, sm: 0.875rem, base: 1rem
lg: 1.125rem, xl: 1.25rem, 2xl: 1.5rem
3xl: 1.875rem, 4xl: 2.25rem
```

### Spacing Scale
```
Based on 4px unit:
1: 0.25rem (4px), 2: 0.5rem (8px)
3: 0.75rem (12px), 4: 1rem (16px)
6: 1.5rem (24px), 8: 2rem (32px)
```

---

## Step 4: Output Formats

### CSS Variables
```css
:root {
  --color-primary: #3B82F6;
  --color-primary-hover: #2563EB;
  --space-1: 0.25rem;
  --radius-md: 0.375rem;
}
```

### Tailwind Config
```js
module.exports = {
  theme: {
    extend: {
      colors: {
        primary: '#3B82F6',
      },
      spacing: {
        '1': '0.25rem',
      },
    },
  },
}
```

### JSON (Style Dictionary)
```json
{
  "color": {
    "primary": { "value": "#3B82F6" }
  },
  "spacing": {
    "1": { "value": "0.25rem" }
  }
}
```

---

## Token Naming Conventions

| Type | Pattern | Example |
|------|---------|---------|
| Color | `--color-{name}` | `--color-primary` |
| Spacing | `--space-{scale}` | `--space-4` |
| Font size | `--font-size-{name}` | `--font-size-lg` |
| Radius | `--radius-{size}` | `--radius-md` |
| Shadow | `--shadow-{size}` | `--shadow-lg` |

---

## Best Practices

1. **Use semantic names** for application tokens (not raw values)
   - `--color-text-primary` not `--gray-900`

2. **Layer tokens:**
   - Primitive: `--blue-500`
   - Semantic: `--color-primary` (references `--blue-500`)
   - Component: `--button-bg` (references `--color-primary`)

3. **Document usage** for every token

4. **Version tokens** when making breaking changes
