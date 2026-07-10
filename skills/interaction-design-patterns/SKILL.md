---
name: interaction-design-patterns
description: >
  Common interaction design patterns: forms, navigation, search, filters, onboarding, empty states, error states, loading states, and microinteractions. Each pattern includes the problem it solves, when to use it, and common anti-patterns. Use when designing user flows, choosing interaction patterns, or reviewing interaction quality. Triggers on: "interaction pattern", "form design", "navigation pattern", "onboarding", "empty state", "error state", "microinteraction", "user flow".
---

# Interaction Design Patterns

Common interaction patterns with the problems they solve, when to use them, and what to avoid.

## Forms

### Best Practices
- **Label above input** — fastest scanning. Left-aligned labels are slower but good for long forms.
- **One column** — multi-column forms confuse the eye path.
- **Group related fields** — use fieldsets with clear headings.
- **Inline validation** — validate on blur, not on every keystroke.
- **Show password toggle** — always. Password managers handle generation.
- **Progress indicator** for multi-step forms — show steps completed and remaining.

### Anti-Patterns
- Placeholder as label — disappears when user types, breaks accessibility.
- Reset button — no one wants this. Users accidentally click it.
- Mark optional fields — mark required fields with `*` instead. Everything else is optional.

## Navigation

### Types
| Pattern | Best For | Avoid When |
|---------|----------|------------|
| **Tab bar** | 3-5 top-level sections | >5 items, deep hierarchies |
| **Sidebar** | Complex products, many sections | Simple apps with 2-3 sections |
| **Hamburger** | Mobile, secondary navigation | Primary navigation on desktop |
| **Breadcrumbs** | Deep hierarchies (3+ levels) | Flat structures |
| **Command palette** | Power users, keyboard-first | Consumer apps, mobile-only |

### Rules
- **3-click rule** — any content within 3 taps/clicks from home.
- **Current state visible** — users must always know where they are.
- **Consistent placement** — nav doesn't move between screens.

## Search

### Best Practices
- **Prominent placement** — top-center or top-right.
- **Instant results** — show suggestions as user types (debounced 150-300ms).
- **Recent searches** — show below the input before user types.
- **Empty state helpful** — "No results for 'xyz'. Try: alternative spellings, broader terms."
- **Filter + sort** — visible when results >1 page.

## Empty States

Three types, each requiring different treatment:

| Type | Example | Design |
|------|---------|--------|
| **First-use** | New user, no data yet | Illustration + what this feature does + CTA to add first item |
| **User-cleared** | User deleted everything | Confirmation message + undo option + CTA to add new |
| **No results** | Search/filter returns nothing | Clear explanation + suggestions + reset filter button |

## Error States

### Structure
1. **What happened** — plain language, no jargon. "Payment failed" not "Error 402: PaymentException"
2. **Why it happened** — be specific. "Your card was declined by your bank."
3. **How to fix it** — actionable next step. "Try a different card or contact your bank."

### Rules
- Error color (red) for the message, not the entire field. Red field = scary.
- Inline errors near the field, not a toast at the top.
- Preserve user input. Never clear a form on error.

## Loading States

| Duration | Pattern |
|----------|---------|
| <100ms | Instant — no feedback needed |
| 100ms-1s | Skeleton screen |
| 1-5s | Skeleton + progress indicator |
| 5-10s | Progress + "This is taking longer than expected" |
| >10s | Progress + cancel option + estimated time |

## Microinteractions

Four components: **Trigger** → **Rules** → **Feedback** → **Loop/Mode**

### Examples
- **Button press** — scale to 0.97 on press, release on lift. Duration: 100ms.
- **Toggle switch** — thumb slides, color transitions. Duration: 200ms.
- **Toast notification** — slide in from bottom, auto-dismiss after 4s with progress bar.
- **Success checkmark** — stroke draws itself on completion. Duration: 300ms.

### Rules
- **Purposeful** — every animation serves a function, never decorative.
- **Subtle** — 200-300ms max. Users shouldn't notice the animation, just the result.
- **Consistent** — same element, same animation every time.

## Related Skills

- For visual design fundamentals → use `ui-visual-design`
- For motion details → use `jakubkrehel/make-interfaces-feel-better`
- For mobile-specific patterns → use `mobile-touch-design`
- For accessibility → use `a11y-accessibility-design`
