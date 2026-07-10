---
name: mobile-touch-design
description: >
  Design principles for touch interfaces: thumb zones, touch target sizing, gesture patterns, responsive breakpoints, and mobile-first workflow. Covers iOS HIG and Material Design touch guidelines, Fitts's Law for thumbs, and the complete mobile design checklist. Use when designing or reviewing mobile interfaces, touch-first products, or responsive layouts. Triggers on: "mobile design", "touch targets", "thumb zone", "responsive design", "gesture design", "mobile-first", "iOS design", "Android design".
---

# Mobile Touch Design

Design principles for touch interfaces. Thumbs, not mice.

## Touch Targets

### Minimum Sizes (by guideline)
| Guideline | Minimum Size | Spacing |
|-----------|-------------|---------|
| Apple iOS HIG | 44×44pt | — |
| Google Material | 48×48dp | 8dp between targets |
| Nielsen Norman Group | 1×1cm (0.4×0.4in) | 2mm minimum |
| WCAG 2.2 AA | 24×24 CSS px | Adequate spacing |

### Practical Rule
- **Primary actions: 48×48dp minimum** (optimal, not just minimum).
- **Secondary actions: 44×44pt minimum** (Apple standard).
- **Icons can be small** if the hit area is padded to 48dp. Use `padding` to expand tappable area without enlarging the visual.

## Thumb Zones

The natural one-handed reach area:

```
┌──────────────────────────────┐
│  🔴 Hard        🔴 Hard      │  ← Top corners: hardest to reach
│                              │
│  🟡 OK          🟡 OK        │  ← Middle: comfortable with stretch
│                              │
│  🟢 Easy        🟢 Easy      │  ← Bottom: natural thumb zone
│  ┌────────────────────────┐  │
│  │     [ Bottom Nav ]     │  │  ← Primary actions go here
│  └────────────────────────┘  │
└──────────────────────────────┘
```

### Placement Rules
- **Primary CTAs** → Bottom center or bottom-right (natural thumb rest).
- **Destructive actions** → Top corners (harder to reach accidentally).
- **Navigation** → Bottom tab bar, not top hamburger.
- **Back button** → Top-left is iOS standard; bottom gesture is modern alternative.

## Gesture Patterns

| Gesture | Used For | Always Provide Alternative |
|---------|----------|---------------------------|
| **Tap** | Primary action, selection | — |
| **Swipe horizontal** | Navigate between screens, dismiss items | Buttons for the same action |
| **Swipe vertical** | Scroll, pull-to-refresh | — |
| **Pinch** | Zoom in/out | +/- buttons or double-tap |
| **Long press** | Context menu, multi-select | Right-click menu or overflow button |
| **Pull down** | Dismiss modal, refresh | Close button or refresh button |

### Gesture Rules
- **Never rely solely on gestures** — always provide a tap alternative.
- **Teach gestures once** — show a tutorial overlay on first use, then never again.
- **Visual affordance** — cards that swipe should show a peek of what's behind.

## Responsive Breakpoints

| Breakpoint | Width | Target |
|------------|-------|--------|
| Mobile | <640px | Phone portrait/landscape |
| Tablet | 640-1024px | Tablet, large phone landscape |
| Desktop | 1024-1440px | Laptop, small monitor |
| Wide | >1440px | Large monitor |

### Mobile-First Approach
1. Design the mobile layout first — forces content prioritization.
2. Add breakpoints as the viewport grows — don't start desktop and shrink.
3. Use `min-width` media queries (add complexity as space increases).
4. Test on real devices — emulators don't show touch targets or thumb reach accurately.

## Typography for Mobile

| Element | Size | Notes |
|---------|------|-------|
| Body text | 16px (1rem) | Minimum. iOS auto-zooms inputs <16px. |
| Line height | 1.5× | Generous leading for readability while scrolling. |
| Headings | Use weight, not just size | Bold H2 can hierarchy over light H1. Saves space. |
| Tap targets for links | ≥44×44pt | Inline links are hard to tap. Use buttons instead. |

## Mobile Design Checklist

- [ ] All primary touch targets ≥48dp
- [ ] 8dp spacing between interactive elements
- [ ] Primary CTAs in bottom 2/3 of screen
- [ ] Body text ≥16px (prevents iOS zoom)
- [ ] Correct input types (tel, email, number) for keyboard
- [ ] Keyboard doesn't cover input field (scroll on focus)
- [ ] Contrast ≥4.5:1 for outdoor visibility
- [ ] All buttons have visible `:active` state
- [ ] Gestures have visual cues (peek, pagination dots)
- [ ] Destructive actions placed out of easy thumb reach
- [ ] No hover-only functionality
- [ ] Tested on real devices, not just emulators

## Related Skills

- For visual design fundamentals → use `ui-visual-design`
- For interaction patterns → use `interaction-design-patterns`
- For accessibility → use `a11y-accessibility-design`
