---
name: onboarding-ux
description: >
  Design first-time experiences that activate users. Covers progressive
  disclosure, activation milestones, persona-based onboarding, empty states,
  checklists, tooltips, and measuring onboarding success. Use when designing
  first-time user experiences, improving activation rates, or reducing early
  churn. Triggers on: "onboarding", "first time user experience", "FTUE",
  "user activation", "progressive disclosure", "empty state", "product tour",
  "user checklist".
---

# Onboarding UX

Onboarding is the bridge between signup and value. Poor onboarding is the single largest driver of early churn.

> "The shorter the path from sign-up to 'I see why this matters,' the higher your activation rate." — Samuel Hulick

## The Activation Framework

### Define Your Activation Milestone
The single action or outcome that signals a user has found value. Examples:
- Slack: sent first message
- Dropbox: uploaded first file
- Canva: created first design
- Airbnb: completed first booking

**Rule:** Build backward from this milestone. Strip everything that doesn't advance the user toward it.

### The 3-Phase Onboarding Model

| Phase | When | Goal | Patterns |
|-------|------|------|----------|
| **Welcome & Segment** | First 30 seconds | Set direction, personalize | Welcome message, persona survey, branched paths |
| **Guided Activation** | First session | Reach activation milestone | Checklists, tooltips, product tours, empty states |
| **Progressive Discovery** | Days 2-30 | Introduce advanced features | Contextual tips, feature announcements, re-engagement |

## Onboarding Patterns

### Pattern: Welcome Message
A simple greeting that sets direction. Not a feature tour.

**Good:** "Welcome to Acme. Let's connect your calendar so you can start saving time."
**Bad:** "Welcome! Here are 12 features you can explore."

### Pattern: Persona-Based Branching
Ask one segmentation question, route to different experiences.

**Example:** ConvertKit asks: "Are you migrating from another platform or starting fresh?" → different paths.

**Rules:**
- 2-5 options max
- Ask after signup, not before
- Make it feel like personalization, not a survey

### Pattern: Checklist
A list of setup tasks with progress indicator.

**Best practices:**
- 3-5 items max (more = overwhelming)
- Mix easy and medium tasks (builds momentum)
- Show progress visually (Zeigarnik effect: incomplete tasks create pull)
- First item should be trivial to complete

### Pattern: Contextual Tooltips
Highlight features when users first encounter them.

**Rules:**
- One tooltip per new screen
- Point to one element, not the whole UI
- Always show how to dismiss
- Never show the same tooltip twice

### Pattern: Progressive Disclosure
Introduce features as they become relevant across multiple sessions.

**Example:** Grammarly starts with grammar checking, introduces tone detection after a few sessions, then style guides.

**Rule:** Don't show everything on Day 1. Stage across the first week.

### Pattern: Empty States
Turn blank screens into starting lines.

| Type | Approach |
|------|----------|
| **First-use** | Instruction + CTA: "Create your first project" |
| **User-cleared** | Confirmation + undo: "You deleted all items. Undo?" |
| **No results** | Suggestions + reset: "No results. Try: broader terms, fewer filters" |

**Rule:** Every empty screen a new user might see needs a clear next action.

## Measurement

### Key Metrics
| Metric | What It Measures | Target |
|--------|------------------|--------|
| **Activation rate** | % reaching activation milestone | >37.5% (2025 SaaS median) |
| **Time to first value** | Minutes from signup to activation | As low as possible |
| **Tour completion rate** | % finishing onboarding flow | Context-dependent |
| **Session 2 return rate** | % who come back | >40% |
| **Day 7 retention** | % active after 7 days | Benchmark against median |

### Diagnostic Signals
| Signal | Problem | Fix |
|--------|---------|-----|
| High tour completion, low activation | Tour disconnected from value | Connect tour steps to activation actions |
| Activation varies by segment | One-size-fits-all flow | Add persona-based branching |
| Session 2 return <40% | No re-engagement plan | Add behavioral triggers for days 2-3 |
| >5 tour steps, completion <50% | Flow too long | Cut to 5 steps max |

## Common Mistakes

- **Front-loading everything** — showing all features on Day 1
- **Mandatory tours** — users want to explore, not be briefed
- **Measuring completion, not activation** — a completed tour is not success
- **No skip option** — some users don't need onboarding
- **One flow for all users** — different segments need different paths

## Related Skills

- For empty state patterns → use `interaction-design-patterns`
- For progressive disclosure principles → use `information-architecture`
- For UX writing in onboarding → use `ux-writing-content-design`
