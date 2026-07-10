---
name: design-systems
description: >
  Build and maintain scalable design systems: design tokens, component libraries,
  naming conventions, governance, and design-to-code pipelines. Covers Figma
  variables, token architecture, atomic design, component APIs, versioning, and
  team workflows. Use when creating a design system, scaling an existing one,
  or establishing design-engineering alignment. Triggers on: "design system",
  "design tokens", "component library", "token naming", "Figma variables",
  "atomic design", "design ops", "design-to-code".
---

# Design Systems

A design system is a single source of truth for design and engineering — tokens, components, patterns, and governance that keep products consistent at scale.

## Design Tokens

The atomic values that feed every component. The single source of truth.

### Token Tiers (3-Layer Model)

| Tier | Purpose | Examples |
|------|---------|----------|
| **Primitive** | Raw values, no semantic meaning | `#0066CC`, `16px`, `0.25rem` |
| **Semantic** | Meaning-based, context-aware | `color-action-primary`, `spacing-section-md` |
| **Component** | Component-specific assignments | `button-bg-primary`, `input-border-focus` |

### Token Categories
- **Color** — brand, neutral, semantic (success/warning/error), alpha
- **Spacing** — inset, stack, inline, spacing scale
- **Typography** — font family, size, weight, line height, letter spacing
- **Elevation** — shadow, z-index, overlay opacity
- **Border** — radius, width, style
- **Motion** — duration, easing curve
- **Breakpoint** — responsive width thresholds

### Naming Convention
Use consistent, predictable naming. Two popular approaches:

**Category/Property/Variant (recommended)**
```
color/background/primary
color/background/primary-hover
spacing/inset/md
typography/font-size/body
```

**Dot-notation (code-friendly)**
```
color.bg.primary
color.bg.primaryHover
spacing.inset.md
```

### Token Governance
- **Assign token owners** — each category has a responsible person
- **Version tokens** — use semantic versioning (breaking.addition.fix)
- **Document decisions** — every token needs a rationale
- **Audit quarterly** — retire unused tokens, resolve duplicates
- **Never hardcode** — everything consumed by components must be a token

## Component Architecture

### Atomic Design Hierarchy

| Level | Description | Example |
|-------|-------------|---------|
| **Atoms** | Indivisible building blocks | Button, Input, Label, Icon |
| **Molecules** | Simple component groups | Search bar (input + button), Form field (label + input + error) |
| **Organisms** | Complex, distinct sections | Header, Card, Navigation bar |
| **Templates** | Page-level layouts without content | Product page template, Dashboard template |
| **Pages** | Templates populated with real content | Actual product page with real data |

### Component API Design
Every component needs a clear, documented API:

```typescript
interface ButtonProps {
  variant: 'primary' | 'secondary' | 'tertiary' | 'danger';
  size: 'sm' | 'md' | 'lg';
  isDisabled?: boolean;
  isLoading?: boolean;
  onPress: () => void;
  children: ReactNode;
  icon?: IconName;        // optional icon
  iconPosition?: 'start' | 'end';
}
```

### Component Checklist
- [ ] All states defined: default, hover, active, focus, disabled, loading
- [ ] Accessibility: keyboard operable, screen reader labels, focus visible
- [ ] Responsive behavior documented
- [ ] Dark mode variants specified
- [ ] Usage guidelines written
- [ ] Do/don't examples provided
- [ ] Edge cases handled (empty, error, overflow)

## Figma-to-Code Pipeline

### Modern Workflow (2025)
1. **Design in Figma** using variables and components
2. **Variables sync to Token Studio** or Figma REST API
3. **Tokens export as JSON** via Style Dictionary or Tokens Studio
4. **JSON transforms to code** (CSS variables, JS constants, Swift, Android XML)
5. **Components built in code** consuming the same tokens
6. **Storybook** documents components with live examples

### Tools
| Tool | Purpose |
|------|---------|
| Token Studio | Token management in Figma |
| Style Dictionary | Transform tokens to any platform |
| Code Connect | Link Figma components to code |
| Storybook | Component documentation |
| Chromatic | Visual regression testing |

## Governance Model

### Roles
| Role | Responsibility |
|------|----------------|
| **Design System Lead** | Strategy, roadmap, stakeholder alignment |
| **Token Owner** | Maintains token category, reviews changes |
| **Component Owner** | Maintains component, ensures quality |
| **DesignOps** | Process, tooling, adoption metrics |

### Contribution Model
Three approaches, pick one based on team size:

1. **Centralized** — dedicated team maintains everything (enterprise)
2. **Federated** — contributions from product teams, reviewed by core team (mid-size)
3. **Open source** — anyone can contribute, PR review process (small/startups)

### Change Process
1. Proposal submitted (RFC or ticket)
2. Reviewed by component/token owner
3. Designed and documented
4. Implemented in code
5. Communicated to consumers (changelog, migration guide)
6. Deprecated old version with timeline

## Anti-Patterns

- **One-off overrides** — teams bypassing tokens for "just this one case"
- **Undocumented components** — built but not in the system
- **Breaking changes without migration** — updating without guidance
- **Design without code parity** — Figma looks different from production
- **Too many variants** — component with 50+ variants is unmaintainable

## Related Skills

- For visual design fundamentals → use `ui-visual-design`
- For dark mode specifics → use `dark-mode-design`
- For accessibility in systems → use `a11y-accessibility-design`
