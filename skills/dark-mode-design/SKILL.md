---
name: dark-mode-design
description: >
  Implement dark mode as a first-class citizen, not an afterthought. Covers
  elevation through luminance, surface hierarchy, color adaptation, image
  handling, and token architecture for multi-mode design systems. Use when
  implementing dark mode, defining dark theme tokens, or testing dark themes.
  Triggers on: "dark mode", "dark theme", "elevation", "night mode",
  "theme switching", "dark UI", "OLED design".
---

# Dark Mode Design

Dark mode is not light mode inverted. It's a distinct design that requires its own surface hierarchy, color logic, and testing approach.

## The Core Principle: Elevation Through Luminance

On light backgrounds, elevation is shown with shadows (darker = higher). On dark backgrounds, shadows don't read. Instead, **elevated surfaces get lighter**.

| Elevation Level | Light Mode | Dark Mode |
|-----------------|------------|-----------|
| **Base** | #FFFFFF | #121212 |
| **Surface (cards)** | #FFFFFF + shadow | #1E1E1E |
| **Raised (dropdowns)** | #FFFFFF + larger shadow | #272727 |
| **Overlay (modals)** | #FFFFFF + backdrop blur | #323232 |

## Surface Tokens (Minimum 4 Levels)

```
dark/surface/base      = #121212  (deepest, page background)
dark/surface/raised    = #1E1E1E  (cards, panels)
dark/surface/elevated  = #272727  (dropdowns, menus, hover)
dark/surface/overlay   = #323232  (modals, dialogs, tooltips)
```

### Never Use Pure Black
Pure black (#000000) causes:
- Excessive contrast with white text (halation effect)
- No room for elevation hierarchy
- Lost depth cues on OLED (true black = off = no shadow visible)

**Use #121212 to #1E1E1E as base.**

## Color Adaptation

### Brand Colors
- Desaturate by 10-20% for dark surfaces (fully saturated colors vibrate)
- Lighten primary for better contrast on dark backgrounds
- Test all brand colors on dark surfaces

### Semantic Colors
| Intent | Light Mode | Dark Mode |
|--------|------------|-----------|
| **Error** | #D32F2F | #EF5350 (lighter for visibility) |
| **Warning** | #F9A825 | #FFD54F |
| **Success** | #388E3C | #66BB6A |

### Text Colors
- **Primary text:** #E0E0E0 (87% white, not pure white)
- **Secondary text:** #A0A0A0 (60% white)
- **Disabled text:** #6E6E6E (38% white)
- **Pure white (#FFFFFF):** reserved for emphasis only

## Image & Media Handling

| Element | Treatment |
|---------|-----------|
| **Photos** | Slight brightness reduction or subtle overlay to prevent harsh contrast |
| **Transparent PNGs** | Add subtle background behind dark-on-transparent assets |
| **SVG icons** | Use `currentColor` so they adapt automatically |
| **Illustrations** | Provide dark-mode variants or apply brightness filter |

## Shadow Replacement

On dark backgrounds, replace shadows with:

| Instead of... | Use... |
|---------------|--------|
| `box-shadow: 0 2px 4px rgba(0,0,0,0.2)` | Lighter surface color + subtle border |
| Large drop shadows | Surface color change + 1px border in `rgba(255,255,255,0.05)` |
| Inner shadows | Slightly darker tint on the surface |

## Implementation Architecture

### CSS Custom Properties Approach
```css
:root {
  --surface-base: #FFFFFF;
  --surface-raised: #FFFFFF;
  --text-primary: rgba(0, 0, 0, 0.87);
  /* shadows for light mode */
}

[data-theme="dark"] {
  --surface-base: #121212;
  --surface-raised: #1E1E1E;
  --text-primary: rgba(255, 255, 255, 0.87);
  /* borders instead of shadows */
}
```

### Tailwind CSS
```javascript
// tailwind.config.js
module.exports = {
  darkMode: 'class', // or 'media'
  // dark: prefix works automatically
}
```

### Respecting System Preference
Always default to `prefers-color-scheme`, with optional manual override:
1. Check `prefers-color-scheme: dark`
2. Allow user toggle (stored in localStorage)
3. Apply theme class before render to prevent flash

## Testing Checklist

- [ ] All text meets WCAG AA on dark surfaces (4.5:1 for body)
- [ ] All interactive states visible (hover, focus, active, disabled)
- [ ] No pure black backgrounds (#000000)
- [ ] Images don't become invisible or harsh
- [ ] Shadows replaced with borders/surface changes
- [ ] Tested on OLED and LCD screens
- [ ] Tested with color blindness simulators
- [ ] Smooth theme transition (no jarring flash)
- [ ] Third-party embeds handled (may need overrides)

## Related Skills

- For design tokens → use `design-systems`
- For color and contrast → use `ui-visual-design`
- For accessibility testing → use `a11y-accessibility-design`
