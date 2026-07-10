---
name: conversion-growth-design
description: >
  Apply behavioral psychology to design for action. Covers landing page design,
  CRO principles, behavioral psychology frameworks, A/B testing strategy,
  friction reduction, and the persuasion toolkit. Use when optimizing for
  conversions, designing landing pages, or building growth features. Triggers
  on: "conversion optimization", "CRO", "landing page", "growth design",
  "behavioral psychology", "persuasion design", "funnel optimization",
  "A/B testing design".
---

# Conversion & Growth Design

Apply behavioral psychology to design for action. Conversion is not manipulation — it's removing friction between user intent and the outcome they want.

## Core Frameworks

### Fogg Behavior Model
Behavior = Motivation × Ability × Prompt

All three must converge:
- **Motivation** — does the user want this? (emotion, anticipation, belonging)
- **Ability** — is it easy to do? (time, money, effort, skill)
- **Prompt** — is there a clear call to action? (trigger, nudge, reminder)

**Design implication:** If motivation is low, increase ability (make it effortless). If ability is low, increase motivation (show value). Always provide a clear prompt.

### Hick's Law
More choices = longer decision time = more abandonment.

**Apply:** One primary action per screen. Highlight the recommended option. Collapse advanced options.

### Cognitive Load Theory
Working memory is limited. Reduce mental effort.

| Source of Load | Fix |
|----------------|-----|
| Too much information | Progressive disclosure |
| Unfamiliar terms | Plain language |
| Too many decisions | Smart defaults, recommended option |
| Visual clutter | White space, clear hierarchy |
| Task switching | Linear flow, one thing at a time |

## Landing Page Design

### The 5-Second Rule
A visitor decides whether to stay or leave in 5 seconds. Above the fold must communicate:
1. What this is
2. Why it matters to them
3. What to do next

### Above-the-Fold Structure

```
┌─────────────────────────────────────┐
│  Logo          Nav          [CTA]   │  ← Navigation minimal
│                                     │
│  Headline: The value proposition    │  ← One clear benefit
│  Subhead: How it works (brief)      │  ← Clarify, not sell
│                                     │
│  [Primary CTA]  [Secondary CTA]     │  ← One primary action
│                                     │
│  [Visual: product/demo/social proof]│  ← Show, don't tell
└─────────────────────────────────────┘
```

### Headline Formula
[Outcome user wants] + [How you deliver it] + [Timeframe if impressive]

Examples:
- "Ship your SaaS in weeks, not months"
- "Automatic backups that never slow you down"
- "The project management tool that actually gets used"

### CTA Design
- **Use first-person** — "Start my free trial" outperforms "Start your free trial"
- **Lead with a verb** — "Get", "Start", "Create", "Download"
- **Include the benefit** — "Get the guide" not "Submit"
- **One primary CTA per section** — multiple CTAs compete and confuse

### Persuasion Elements

| Element | When to Use | Example |
|---------|-------------|---------|
| **Social proof** | Above the fold, near CTAs | "Join 10,000+ teams" / logos |
| **Testimonials** | After feature descriptions | Quote + photo + title + company |
| **Risk reversal** | Near purchase/signup | "30-day money back", "Cancel anytime" |
| **Specificity** | Instead of vague claims | "Save 5 hours per week" not "Save time" |
| **Scarcity** | Only if genuine | "Only 3 seats left at this price" |
| **Authority** | B2B, professional tools | Certifications, industry awards |

## Friction Reduction

### Form Optimization
| Technique | Impact |
|-----------|--------|
| Reduce fields | Each field reduces completion ~10% |
| Inline validation | Validate on blur, not submit |
| Show password toggle | Always |
| Autofill enabled | `autocomplete` attributes on all fields |
| Progress indicator | For multi-step forms |
| Address lookup | Auto-complete from postal code |

### Checkout Optimization
- Guest checkout option (don't force account creation)
- Summary visible throughout (what, how much, when)
- Security signals near payment (SSL badge, encryption note)
- Multiple payment options
- Clear error messages with inline correction

## A/B Testing for Conversion

### Hypothesis Structure
"If we change [element] for [audience], we expect [result] because [behavioral reason]."

### What to Test (Priority Order)
1. Headline and value proposition
2. CTA text and placement
3. Social proof placement and format
4. Form length and fields
5. Page length (short vs. long)
6. Visual hierarchy
7. Color (last — usually smallest impact)

### Statistical Rigor
- Run for complete business cycles (full weeks)
- Reach 95% statistical significance
- Check guardrail metrics (don't improve conversion at expense of retention)
- Segment results (what works for mobile may not for desktop)

## Dark Side Warning

Many growth tactics are dark patterns in disguise. Before implementing any persuasion technique, run it through the ethical filter:

- Is this genuinely helping the user?
- Would I be comfortable explaining this to a frustrated user?
- Am I creating real value or manufactured urgency?
- Can users easily undo this action?

If the honest answer to any of these is no, don't do it.

## Related Skills

- For onboarding and activation → use `onboarding-ux`
- For landing page UX writing → use `ux-writing-content-design`
- For ethical boundaries → use `ethical-design-patterns`
- For A/B testing methods → use `user-research-methods`
