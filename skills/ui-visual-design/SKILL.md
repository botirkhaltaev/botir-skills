---
name: ui-visual-design
description: >
  Core visual design principles for interfaces: typography hierarchy, spacing systems, color theory, contrast, and layout fundamentals. Covers type scale construction, the 8pt grid system, accessible contrast ratios, and visual hierarchy techniques. This is about the visual layer — the pixels users see. Use when designing or reviewing the visual appearance of any interface. Triggers on: "typography", "spacing", "color theory", "contrast", "visual hierarchy", "layout fundamentals", "design system basics".
---

# UI Visual Design

Core visual design principles for interfaces. The pixels users see.

## Typography Hierarchy

### Type Scale
Build a type scale with a ratio (1.25 minor third, 1.414 augmented fourth, or 1.5 perfect fifth):

| Token | Size | Weight | Usage |
|-------|------|--------|-------|
| Display | 48-72px | 700 | Hero headlines |
| H1 | 36-48px | 700 | Page titles |
| H2 | 24-32px | 600 | Section headers |
| H3 | 18-20px | 600 | Subsection headers |
| Body | 16px | 400 | Primary reading text |
| Body Small | 14px | 400 | Secondary text, metadata |
| Caption | 12px | 500 | Labels, timestamps |

### Rules
- **Max 2 typefaces** — one for display/headings, one for body. More creates visual chaos.
- **Line height 1.5×** for body text (16px font → 24px line-height). 1.2× for headings.
- **Measure 45-75 characters** per line for body text. Too wide = hard to track.
- **Use weight, not just size** to create hierarchy. Bold H2 can outrank light H1.

## Spacing System

### The 8pt Grid
Base spacing increments on multiples of 8:

| Token | Value | Usage |
|-------|-------|-------|
| xs | 4px | Tight inline spacing, icon gaps |
| sm | 8px | Default gap between related elements |
| md | 16px | Section padding, card internal spacing |
| lg | 24px | Between distinct sections |
| xl | 32px | Page-level padding |
| 2xl | 48px | Major section separators |
| 3xl | 64px | Hero section breathing room |

### Rules
- Use a **consistent multiplier** (4px, 8px, or 16px) across the entire product.
- **White space is not empty** — it separates, groups, and creates hierarchy.
- **Group by proximity** — related elements are closer together than unrelated ones.
- **Don't mix systems** — if you use 8pt, don't randomly use 13px or 19px.

## Color & Contrast

### Accessible Contrast (WCAG AA)
| Element | Minimum Ratio |
|---------|--------------|
| Body text (<18px) | 4.5:1 |
| Large text (≥18px bold or 24px) | 3:1 |
| UI components (buttons, icons) | 3:1 |

### Color Roles
| Role | Count | Purpose |
|------|-------|---------|
| **Primary** | 1 | Main CTA, key actions, brand color |
| **Secondary** | 1 | Alternative actions, less emphasis |
| **Neutral** | 3-4 | Text, backgrounds, borders (grays) |
| **Semantic** | 3 | Success (green), Warning (amber), Error (red) |

### Rules
- **60-30-10 rule** — 60% neutral, 30% primary, 10% accent.
- Never rely on color alone to convey information. Always pair with icon, text, or pattern.
- Test dark mode early. Colors that work on white may fail on black.

## Visual Hierarchy Techniques

1. **Size** — Larger elements draw more attention.
2. **Weight** — Bold text outcompetes regular.
3. **Color** — High contrast draws the eye. Use sparingly.
4. **Position** — Top-left (in LTR) is highest priority.
5. **White space** — Isolated elements stand out more.
6. **Depth** — Shadows and layering create elevation hierarchy.

### The Z-Pattern (Western reading)
Users scan pages in a Z shape: top-left → top-right → diagonal → bottom-left → bottom-right. Place key info at the corners of the Z.

## Related Skills

- For motion and animation → use `jakubkrehel/make-interfaces-feel-better`
- For interaction patterns → use `interaction-design-patterns`
- For mobile-specific design → use `mobile-touch-design`
- For accessibility → use `a11y-accessibility-design`
