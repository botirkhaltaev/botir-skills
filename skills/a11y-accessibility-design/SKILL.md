---
name: a11y-accessibility-design
description: >
  Web accessibility (a11y) design fundamentals based on WCAG 2.2 AA. Covers the POUR principles (Perceivable, Operable, Understandable, Robust), color contrast requirements, keyboard navigation, focus management, screen reader support, and a practical pre-ship checklist. Use when designing accessible interfaces, auditing for a11y compliance, or building inclusive products. Triggers on: "accessibility", "a11y", "WCAG", "screen reader", "keyboard navigation", "contrast ratio", "inclusive design", "accessible UI".
---

# Accessibility (a11y) Design

Web accessibility fundamentals based on WCAG 2.2 AA. Designing for everyone — including users with visual, auditory, cognitive, and motor disabilities.

> "The power of the Web is in its universality. Access by everyone regardless of disability is an essential aspect." — Tim Berners-Lee

## The POUR Principles

### Perceivable — Users can perceive the content
- **Color contrast** — Text ≥4.5:1 (AA), large text ≥3:1, UI components ≥3:1.
- **Alt text** — All informative images have descriptive alt text. Decorative images use `alt=""`.
- **Don't rely on color alone** — Pair color with icon, text label, or pattern.
- **Text resizing** — Content readable at 200% zoom without horizontal scroll.

### Operable — Users can operate the interface
- **Keyboard accessible** — All interactive elements work with Tab, Enter, Space, Escape.
- **Focus visible** — Clear focus indicator (outline, ring, or highlight) on every focusable element.
- **No keyboard traps** — User can Tab away from any element.
- **Touch targets** — Minimum 24×24 CSS px (WCAG 2.2 AA) or 44×44pt (iOS).

### Understandable — Users can understand the content
- **Plain language** — Avoid jargon. Write at 8th-grade reading level for broad audiences.
- **Consistent navigation** — Same nav in same place on every page.
- **Error messages** — Specific, actionable. "Email is required" not "Invalid input."
- **Form labels** — Every input has an associated `<label>`.

### Robust — Content works with assistive technology
- **Semantic HTML** — Use `<button>` for buttons, `<a>` for links, `<nav>`, `<main>`, `<header>`.
- **ARIA sparingly** — Native HTML is always preferred. Use ARIA only when HTML falls short.
- **Valid markup** — Pass HTML validation. Broken markup breaks screen readers.

## Color & Contrast Checklist

| Element | Minimum Ratio (AA) | Tool |
|---------|-------------------|------|
| Body text (<18px) | 4.5:1 | WebAIM Contrast Checker |
| Large text (≥18px bold / 24px) | 3:1 | WebAIM Contrast Checker |
| UI components (buttons, icons) | 3:1 | Stark, A11y - Color Contrast Checker |
| Focus indicators | 3:1 against adjacent colors | Manual check |

### Testing
- Use **Sim Daltonism** or Chrome DevTools → Rendering → Emulate vision deficiencies.
- Test in grayscale — if hierarchy breaks without color, it fails.

## Keyboard Navigation Checklist

- [ ] All interactive elements reachable via Tab
- [ ] Tab order follows visual reading order (left-to-right, top-to-bottom)
- [ ] Focus indicator clearly visible on every element
- [ ] No keyboard traps (can Tab away from everything)
- [ ] Skip link provided to bypass repeated navigation
- [ ] Modal traps focus (Tab cycles within modal, Escape closes)
- [ ] Dropdowns operable with arrow keys, Enter to select, Escape to close

## Screen Reader Support

### Semantic HTML (preferred)
```html
<button>Submit</button>        <!-- Screen reader: "Submit, button" -->
<a href="/">Home</a>           <!-- Screen reader: "Home, link" -->
<nav>...</nav>                 <!-- Screen reader: "Navigation landmark" -->
```

### ARIA (when HTML isn't enough)
```html
<div role="button" tabindex="0" aria-pressed="false">
  Toggle
</div>
```

### Live Regions
Announce dynamic updates without moving focus:
```html
<div aria-live="polite" aria-atomic="true">
  3 new notifications
</div>
```

## Focus Management

### Rules
- **Never remove focus outline** — use `:focus-visible` to show it only for keyboard.
- **Trap focus in modals** — Tab cycles within, doesn't escape to background.
- **Return focus** — When modal closes, focus returns to the element that opened it.
- **Skip links** — First focusable element: "Skip to main content" link.

### CSS Pattern
```css
/* Show focus ring only for keyboard, not mouse click */
:focus-visible {
  outline: 2px solid #0066cc;
  outline-offset: 2px;
}

/* Remove default focus for mouse users */
:focus:not(:focus-visible) {
  outline: none;
}
```

## Pre-Ship Accessibility Checklist

### Automated (catch ~30% of issues)
- [ ] axe DevTools — no violations
- [ ] Lighthouse a11y score ≥90
- [ ] WAVE — no errors

### Manual (catch the other 70%)
- [ ] Navigate entire app with keyboard only (no mouse)
- [ ] Test with screen reader (NVDA free on Windows, VoiceOver on Mac)
- [ ] Check all color combinations for contrast
- [ ] Zoom to 200% — verify no horizontal scroll, all content visible
- [ ] Test with reduced motion preference enabled
- [ ] Verify touch targets on actual mobile devices

### Content
- [ ] All images have alt text (or empty alt for decorative)
- [ ] All form inputs have labels
- [ ] Error messages are specific and actionable
- [ ] Page language declared (`<html lang="en">`)
- [ ] Page titles are unique and descriptive

## Related Skills

- For visual design fundamentals → use `ui-visual-design`
- For mobile-specific design → use `mobile-touch-design`
- For interaction patterns → use `interaction-design-patterns`
