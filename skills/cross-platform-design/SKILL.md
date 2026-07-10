---
name: cross-platform-design
description: >
  Design for multiple platforms while respecting native conventions. Covers iOS
  HIG vs Material Design 3 differences, platform-specific patterns, when to
  diverge for branding, and hybrid approaches. Use when designing for mobile,
  tablet, or cross-platform products. Triggers on: "iOS design", "Android
  design", "Material Design", "cross-platform", "native patterns", "platform
  conventions", "HIG", "mobile platform differences".
---

# Cross-Platform Design

Respect platform conventions while maintaining brand consistency. Users expect your app to feel native to their device.

## Platform Philosophy

| Aspect | iOS HIG | Material Design 3 |
|--------|---------|-------------------|
| **Philosophy** | Clarity, deference, depth | Bold, structured, tactile |
| **Visual style** | Minimal, content-first | Expressive, elevated |
| **Depth** | Layering, blur, translucency | Explicit shadows, Z-axis |
| **Motion** | Subtle, fluid, natural | Purposeful, physics-based |
| **Customization** | Limited (system-driven) | Highly themeable |
| **Typography** | San Francisco | Roboto (customizable) |

## Key Differences

### Navigation

| Element | iOS | Android |
|---------|-----|---------|
| **Back action** | Swipe from left edge OR top-left back button | System back button (always available) |
| **Primary nav** | Bottom tab bar | Bottom nav OR navigation drawer |
| **Primary action** | Top-right button OR bottom tab | Floating Action Button (FAB) |
| **Context menu** | Action sheet (slides up from bottom) | Bottom sheet or overflow menu |

### Typography
- **iOS:** San Francisco (SF Pro). Dynamic Type support required.
- **Android:** Roboto, but customizable via Material Theming.
- **Both:** Use system fonts. Don't embed custom fonts unless brand-critical.

### Touch Targets
- **iOS:** 44×44pt minimum
- **Android:** 48×48dp minimum, 8dp between targets

### System Components

| Component | iOS | Android |
|-----------|-----|---------|
| **Date picker** | Spinning wheel (single) / Calendar (range) | Calendar view |
| **Alert/Dialog** | Alert (centered, max 2 buttons) | Dialog (centered, supports lists) |
| **Snackbar/Toast** | No native snackbar | Snackbar (swipe to dismiss, supports action) |
| **Search** | Search bar in navigation | Search bar can be persistent |
| **Switch** | Rounded, sliding knob | Wider, more pronounced |
| **Picker** | Bottom sheet wheel | Bottom sheet or dialog |

### Status Bar
- **iOS:** Light or dark content, respect safe area
- **Android:** Can be transparent or colored, edge-to-edge encouraged

## The Platform Decision Matrix

| Situation | Recommendation |
|-----------|---------------|
| Building iOS-only | Follow HIG strictly |
| Building Android-only | Follow Material 3 |
| Cross-platform, brand-first | Shared design with platform adaptations |
| Cross-platform, speed-first | Material Design everywhere (faster to implement) |
| Cross-platform, native-first | Separate designs per platform (highest quality) |

## When to Diverge from Platform Guidelines

### Always Follow Platform Conventions
- Navigation patterns (back, tabs, primary action placement)
- System gestures (swipe to go back, edge swipes)
- Permission request timing
- Keyboard behavior and input types

### Can Diverge for Branding
- Color palette (within accessibility constraints)
- Illustration style
- Typography (if brand font is critical)
- Microcopy tone and voice
- Animation personality

## Hybrid Approach Best Practices

1. **Share information architecture** — same structure, different chrome
2. **Maintain feature parity** — same capabilities on both platforms
3. **Adapt navigation** — iOS tabs vs Android bottom nav + drawer
4. **Use platform native components** — date pickers, alerts, share sheets
5. **Test on real devices** — simulators miss gesture feel and performance

## Related Skills

- For mobile touch targets → use `mobile-touch-design`
- For navigation patterns → use `interaction-design-patterns`
- For typography → use `ui-visual-design`
