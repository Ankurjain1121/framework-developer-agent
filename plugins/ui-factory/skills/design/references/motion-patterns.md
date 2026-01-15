# Motion Patterns Library

Reusable animation patterns for common UI interactions.

---

## Core Principles

1. **Purposeful:** Every animation should serve a function
2. **Fast:** 100-300ms for most interactions
3. **Smooth:** 60fps, use transform/opacity only
4. **Respectful:** Honor prefers-reduced-motion

---

## Timing Reference

| Duration | Use For |
|----------|---------|
| 50-100ms | Micro-interactions (hover, press) |
| 150-200ms | Small elements (buttons, icons) |
| 200-300ms | Medium elements (cards, panels) |
| 300-500ms | Large elements (modals, pages) |

---

## Easing Reference

| Easing | Use For | CSS |
|--------|---------|-----|
| ease-out | Entering elements | `cubic-bezier(0, 0, 0.2, 1)` |
| ease-in | Exiting elements | `cubic-bezier(0.4, 0, 1, 1)` |
| ease-in-out | Moving elements | `cubic-bezier(0.4, 0, 0.2, 1)` |
| spring | Bouncy interactions | Custom (Framer) |

---

## Entry Patterns

### Fade In
```css
@keyframes fadeIn {
  from { opacity: 0; }
  to { opacity: 1; }
}
/* Duration: 200ms, ease-out */
```

### Fade Up
```css
@keyframes fadeUp {
  from { opacity: 0; transform: translateY(10px); }
  to { opacity: 1; transform: translateY(0); }
}
/* Duration: 200ms, ease-out */
```

### Scale In
```css
@keyframes scaleIn {
  from { opacity: 0; transform: scale(0.95); }
  to { opacity: 1; transform: scale(1); }
}
/* Duration: 200ms, ease-out */
```

### Slide In (from edge)
```css
@keyframes slideInRight {
  from { transform: translateX(100%); }
  to { transform: translateX(0); }
}
/* Duration: 300ms, ease-out */
```

---

## Exit Patterns

### Fade Out
```css
@keyframes fadeOut {
  from { opacity: 1; }
  to { opacity: 0; }
}
/* Duration: 150ms, ease-in */
```

### Fade Down
```css
@keyframes fadeDown {
  from { opacity: 1; transform: translateY(0); }
  to { opacity: 0; transform: translateY(10px); }
}
/* Duration: 150ms, ease-in */
```

### Scale Out
```css
@keyframes scaleOut {
  from { opacity: 1; transform: scale(1); }
  to { opacity: 0; transform: scale(0.95); }
}
/* Duration: 150ms, ease-in */
```

---

## Micro-interactions

### Button Hover
```css
.button:hover {
  transform: scale(1.02);
  transition: transform 100ms ease-out;
}
```

### Button Press
```css
.button:active {
  transform: scale(0.98);
  transition: transform 50ms ease-in;
}
```

### Input Focus
```css
.input:focus {
  box-shadow: 0 0 0 2px var(--color-primary);
  transition: box-shadow 150ms ease-out;
}
```

### Checkbox Check
```css
@keyframes checkmark {
  from { stroke-dashoffset: 24; }
  to { stroke-dashoffset: 0; }
}
/* Duration: 200ms, ease-out */
```

---

## Feedback Patterns

### Success
```css
@keyframes success {
  0% { transform: scale(0); }
  50% { transform: scale(1.2); }
  100% { transform: scale(1); }
}
/* Duration: 300ms, spring-like */
```

### Error Shake
```css
@keyframes shake {
  0%, 100% { transform: translateX(0); }
  25% { transform: translateX(-4px); }
  75% { transform: translateX(4px); }
}
/* Duration: 300ms, 2 cycles */
```

### Loading Spinner
```css
@keyframes spin {
  from { transform: rotate(0deg); }
  to { transform: rotate(360deg); }
}
/* Duration: 1000ms, linear, infinite */
```

### Skeleton Shimmer
```css
@keyframes shimmer {
  from { background-position: -200% 0; }
  to { background-position: 200% 0; }
}
/* Duration: 1500ms, ease-in-out, infinite */
```

---

## Component Patterns

### Modal
```
Entry: Backdrop fade 200ms + Modal scale 200ms
Exit: Modal scale 150ms + Backdrop fade 150ms
Focus: Trap inside, restore on close
```

### Dropdown
```
Entry: Fade + slide down 150ms
Exit: Fade 100ms
Direction: Open downward (or upward if no space)
```

### Toast
```
Entry: Slide in from edge 200ms
Stay: 3-5 seconds
Exit: Fade out 150ms
Stack: Push others down
```

### Accordion
```
Expand: Height auto 200ms ease-out
Collapse: Height 0 150ms ease-in
Icon: Rotate 180deg
```

---

## Reduced Motion

Always provide fallbacks:

```css
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    transition-duration: 0.01ms !important;
  }
}
```

| Full Motion | Reduced Motion |
|-------------|----------------|
| Fade + transform | Instant appear |
| Spinning loader | Static indicator |
| Bouncing animation | No bounce |
| Parallax effects | Static |

---

## Performance Rules

1. Only animate `transform` and `opacity`
2. Use `will-change` sparingly
3. Avoid animating layout properties (width, height, top, left)
4. Keep concurrent animations < 3
5. Use `requestAnimationFrame` for JS animations
