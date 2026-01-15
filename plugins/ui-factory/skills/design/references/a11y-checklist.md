# Accessibility Checklist

WCAG 2.1 AA compliance checklist for web applications.

---

## Quick Reference

| Category | Key Requirements |
|----------|------------------|
| Perceivable | Alt text, contrast 4.5:1, captions |
| Operable | Keyboard access, no traps, skip links |
| Understandable | Clear labels, error identification |
| Robust | Valid HTML, ARIA when needed |

---

## 1. Perceivable

### 1.1 Text Alternatives
- [ ] All images have alt text
- [ ] Decorative images have `alt=""`
- [ ] Complex images have long descriptions
- [ ] Icons with meaning have accessible names

### 1.2 Time-based Media
- [ ] Videos have captions
- [ ] Audio has transcripts
- [ ] No auto-playing media (or controls provided)

### 1.3 Adaptable
- [ ] Semantic HTML used (headings, lists, tables)
- [ ] Reading order matches visual order
- [ ] Instructions don't rely on sensory characteristics
- [ ] Orientation not locked

### 1.4 Distinguishable
- [ ] Text contrast ratio >= 4.5:1 (regular text)
- [ ] Text contrast ratio >= 3:1 (large text, 18pt+)
- [ ] UI component contrast >= 3:1
- [ ] Text can resize to 200% without loss
- [ ] No horizontal scroll at 320px width
- [ ] Content not conveyed by color alone

---

## 2. Operable

### 2.1 Keyboard Accessible
- [ ] All functionality available via keyboard
- [ ] No keyboard traps
- [ ] Custom widgets follow ARIA patterns
- [ ] Shortcuts can be disabled/remapped

### 2.2 Enough Time
- [ ] Time limits can be extended/disabled
- [ ] Auto-updating content can be paused
- [ ] No time-based content without warning

### 2.3 Seizures and Physical Reactions
- [ ] No content flashes >3 times/second
- [ ] Animation can be disabled (prefers-reduced-motion)

### 2.4 Navigable
- [ ] Skip link to main content
- [ ] Pages have descriptive titles
- [ ] Focus order is logical
- [ ] Link purpose clear from text
- [ ] Multiple ways to find pages
- [ ] Headings and labels descriptive
- [ ] Focus visible at all times

### 2.5 Input Modalities
- [ ] Touch targets >= 44x44px
- [ ] Pointer gestures have alternatives
- [ ] No pointer motion required

---

## 3. Understandable

### 3.1 Readable
- [ ] Page language declared (`lang` attribute)
- [ ] Abbreviations explained on first use
- [ ] Reading level appropriate (or simplified available)

### 3.2 Predictable
- [ ] Focus doesn't trigger unexpected changes
- [ ] Input doesn't trigger unexpected changes
- [ ] Navigation consistent across pages
- [ ] Components identified consistently

### 3.3 Input Assistance
- [ ] Errors identified and described
- [ ] Labels provided for inputs
- [ ] Error suggestions provided
- [ ] Submission can be reviewed/corrected

---

## 4. Robust

### 4.1 Compatible
- [ ] Valid HTML (no duplicate IDs)
- [ ] Complete start/end tags
- [ ] ARIA used correctly
- [ ] Status messages announced

---

## ARIA Quick Reference

### Roles
| Role | Use For |
|------|---------|
| `button` | Clickable actions |
| `link` | Navigation |
| `dialog` | Modals |
| `alert` | Urgent messages |
| `status` | Non-urgent updates |
| `tab`/`tabpanel` | Tab interfaces |
| `menu`/`menuitem` | Dropdown menus |

### Properties
| Property | Use For |
|----------|---------|
| `aria-label` | Accessible name |
| `aria-describedby` | Additional description |
| `aria-expanded` | Collapsible state |
| `aria-selected` | Selection state |
| `aria-hidden` | Hide from AT |
| `aria-live` | Dynamic updates |
| `aria-invalid` | Error state |

---

## Keyboard Patterns

| Component | Expected Keys |
|-----------|---------------|
| Button | Enter, Space to activate |
| Link | Enter to follow |
| Checkbox | Space to toggle |
| Radio | Arrow keys to navigate |
| Select | Arrow keys, Enter to select |
| Tab | Arrow keys between tabs |
| Modal | Escape to close, trap focus |
| Menu | Arrow keys, Escape to close |

---

## Testing Tools

| Tool | What It Tests |
|------|---------------|
| axe DevTools | Automated WCAG checks |
| WAVE | Visual a11y report |
| Lighthouse | Automated audit |
| VoiceOver (Mac) | Screen reader |
| NVDA (Windows) | Screen reader |
| Keyboard only | Manual navigation test |

---

## Common Fixes

| Issue | Fix |
|-------|-----|
| Missing alt text | Add descriptive `alt` attribute |
| Low contrast | Increase to 4.5:1 minimum |
| No focus visible | Add `:focus-visible` styles |
| Keyboard trap | Ensure escape route, manage focus |
| Missing labels | Add `<label>` or `aria-label` |
| Non-semantic HTML | Use proper elements (button, nav, etc.) |
