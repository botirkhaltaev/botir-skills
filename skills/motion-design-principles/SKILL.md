---
name: motion-design-principles
description: >
  Use motion purposefully in interfaces. Covers easing curves, duration
  guidelines, the 12 UI motion principles, choreography, and accessibility
  considerations for animation. Use when adding animations, transitions, or
  micro-interactions to an interface. Triggers on: "animation", "motion design",
  "transition", "easing curve", "micro-interaction", "UI animation",
  "Framer Motion", "page transition".
---

# Motion Design Principles

Motion in UI is not decoration — it's communication. Good animation clarifies relationships, directs attention, and provides feedback.

> "Animation is not about making things bounce. It's about making things understandable." — Pasquale D'Silva

## The 12 Principles of UI Motion

### 1. Purposeful
Every animation serves a function. If removing it changes nothing meaningful, remove it.

| Purpose | Example |
|---------|---------|
| **Orientation** | Where did this element come from? |
| **Feedback** | The system received my input |
| **Relationship** | These elements are connected |
| **Progress** | Something is happening, wait |
| **Delight** | The interface feels alive (use sparingly) |

### 2. Duration: Size Determines Speed
Smaller elements = shorter duration. Larger area transitions = longer duration.

| Element Size | Duration |
|-------------|----------|
| Micro (button press, toggle) | 100-200ms |
| Small (tooltip, dropdown) | 200-300ms |
| Medium (modal, panel) | 300-400ms |
| Large (page transition) | 400-600ms |

**Default:** 200-300ms for most transitions.

### 3. Easing: Nothing Moves Linearly
Linear easing feels robotic. Everything in the real world accelerates and decelerates.

| Easing | Use For | CSS Value |
|--------|---------|-----------|
| **Ease-out** | Elements entering | `cubic-bezier(0, 0, 0.2, 1)` |
| **Ease-in** | Elements exiting | `cubic-bezier(0.4, 0, 1, 1)` |
| **Ease-in-out** | Elements moving within the view | `cubic-bezier(0.4, 0, 0.2, 1)` |
| **Spring** | Interactive/draggable elements | Physics-based |

**Rule:** Entering elements decelerate (ease-out). Exiting elements accelerate (ease-in).

### 4. Spatial Continuity
Elements should originate from and return to logical locations.

- FAB expands from its button location, not from screen center
- Modal slides in from the trigger element's direction
- Back button returns to the previous screen's position

### 5. Stagger
When multiple elements animate, stagger their start times.

| List Size | Stagger Delay |
|-----------|---------------|
| <5 items | 50ms |
| 5-10 items | 30ms |
| >10 items | 20ms |

### 6. Feedback Under 100ms
Input feedback must feel instant. Any delay over 100ms feels laggy.

- Button press: scale to 0.97, release on lift (100ms)
- Touch ripple: start immediately on touch

### 7. Enter, Exit, and Everything Between
Define animations for all states:

| State | Animation |
|-------|-----------|
| **Enter** | Scale up + fade in + ease-out |
| **Exit** | Scale down + fade out + ease-in |
| **Update** | Cross-fade or morph |
| **Loading** | Skeleton pulse or shimmer |

### 8. Reduced Motion
Always respect `prefers-reduced-motion`.

```css
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    transition-duration: 0.01ms !important;
  }
}
```

### 9. Spring Physics for Interaction
Use spring physics (not duration-based easing) for draggable/interactive elements. Springs feel responsive — they react to velocity.

### 10. Scroll Animations Must Earn Their Place
Parallax and scroll-triggered animations often hurt more than help. Use only when:
- They reveal content progressively
- They help users understand spatial relationships
- They don't interfere with reading

### 11. Content Morphing
When content changes, morph between states rather than replacing. Examples:
- Number counters that animate up/down
- Shape morphing between icons
- Layout transitions that reposition elements fluidly

### 12. Only Animate Performant Properties
To maintain 60fps, only animate these properties:

| Animate These | Avoid These |
|---------------|-------------|
| `transform` (translate, scale, rotate) | `width`, `height` |
| `opacity` | `top`, `left`, `right`, `bottom` |
| `filter` (sparingly) | `margin`, `padding` |
| | `box-shadow` |

## Choreography Patterns

### Parent-Child
Parent element triggers, children follow with stagger.

### Sibling
Related elements animate in sequence.

### Independent
Unrelated elements animate independently (no forced synchronization).

## Related Skills

- For microinteraction details → use `interaction-design-patterns`
- For calm technology motion → use `calm-technology-ui`
- For Framer Motion patterns → use `ai-native-ui-design`
