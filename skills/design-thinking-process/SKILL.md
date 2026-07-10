---
name: design-thinking-process
description: >
  Apply design thinking to product problems. Covers the 5-phase framework
  (Empathize, Define, Ideate, Prototype, Test), practical tools for each phase,
  integration with Agile, and artifact creation. Use when starting a new
  project, solving fuzzy problems, or facilitating workshops. Triggers on:
  "design thinking", "discovery", "product discovery", "user centered design",
  "workshop facilitation", "problem framing", "how might we", "design sprint".
---

# Design Thinking Process

A human-centered framework for solving fuzzy problems. Not a linear sequence — a set of modes you move between as you learn.

> "Design thinking is a human-centered approach to innovation that draws from the designer's toolkit to integrate the needs of people, the possibilities of technology, and the requirements for business success." — Tim Brown, IDEO

## The 5 Phases

### 1. Empathize — Understand the Human Need

**Goal:** Deep, emotional understanding of users' context, needs, and pain points.

**Activities:**
- User interviews (open-ended, 5-8 per segment)
- Contextual inquiry (observe in their environment)
- Diary studies (longitudinal, self-reported)
- Competitive analysis (how are they solving this now?)

**Artifacts:**
- Empathy map (Says, Thinks, Does, Feels)
- User personas (behavioral, not demographic)
- Journey map (current state, pain points highlighted)

**Key technique — The 5 Whys:**
When you hear an interesting answer, ask "Why?" up to 5 times to get to the root cause.

> "I don't use the reporting feature." → Why? → "It's too slow." → Why? → "I have to export to Excel first." → Why? → "The filters don't have what I need." → Root cause: Missing filter options.

### 2. Define — Frame the Problem

**Goal:** A clear, actionable problem statement that guides ideation.

**Activities:**
- Synthesize interview findings (affinity mapping)
- Identify patterns and themes
- Reframe problems as opportunities

**Artifact — Point of View (POV) statement:**
```
[User] needs to [need] because [insight]
```

Example: "Small business owners need to reconcile invoices quickly because they spend 3 hours every Friday on bookkeeping instead of serving customers."

**Key technique — "How Might We" (HMW):**
Turn problems into opportunities:
- "Users can't find settings" → "HMW make settings discoverable without cluttering the interface?"
- "The checkout is too long" → "HMW complete checkout in under 30 seconds?"

### 3. Ideate — Generate Many Solutions

**Goal:** Maximum quantity of ideas. Quality comes later.

**Activities:**
- Brainstorming (structured, time-boxed)
- Crazy Eights (8 sketches in 8 minutes)
- SCAMPER checklist (Substitute, Combine, Adapt, Modify, Put to another use, Eliminate, Reverse)
- Worst Possible Idea (reverse brainstorming to unlock creativity)

**Rules:**
- Defer judgment — no "that won't work" during ideation
- Build on others' ideas — "Yes, and..."
- Go for quantity — 100 ideas is better than 10
- Encourage wild ideas — they often spark practical ones

**Artifact:** Idea board with 20-50 ideas, grouped by theme.

### 4. Prototype — Make Ideas Tangible

**Goal:** Cheap, fast representations of the best ideas to test assumptions.

**Fidelity Spectrum:**

| Fidelity | When to Use | Tools |
|----------|-------------|-------|
| **Paper** | Early exploration, flow testing | Pen, paper, sticky notes |
| **Low-fi digital** | Structure and layout | Balsamiq, wireframes |
| **Mid-fi digital** | Interaction and flow | Figma clickable prototype |
| **Hi-fi digital** | Visual design, stakeholder buy-in | Figma, Principle |
| **Coded** | Technical feasibility, microinteractions | HTML/CSS/JS prototype |

**Prototype only what you need to test.** If testing navigation, don't polish visuals. If testing visual appeal, focus on look and feel.

### 5. Test — Learn from Real Users

**Goal:** Validate assumptions, find usability issues, refine understanding.

**Structure:**
1. **Welcome** (2 min) — build rapport, set expectations
2. **Context questions** (5 min) — understand their background
3. **Tasks** (20-30 min) — observe without helping
4. **Debrief** (5 min) — overall impressions

**Key Rules:**
- Test with 5 users — catches ~85% of usability issues (Nielsen)
- Don't defend your design — listen
- Look for patterns, not isolated issues
- One person struggling = noise; three people struggling = signal

## Integration with Agile

| Timeframe | Activity | Output |
|-----------|----------|--------|
| **Week 1-2** | Discovery Sprint (Empathize + Define) | Validated problem, HMW statements |
| **Week 3** | Ideation + Prototyping Workshop | 3 testable prototypes |
| **Week 4** | User Testing (Test) | Insights, validated concept |
| **Week 5-6** | Development Sprint (Agile) | Working software |
| **Ongoing** | Continuous feedback | Iteration fuel |

## Facilitation Tips

- **Time-box everything** — constraints drive creativity
- **Include diverse perspectives** — engineering, design, product, support
- **Visualize everything** — sticky notes, whiteboards, sketches
- **No laptops/phones** — full presence during workshops
- **Vote with dots** — silent, democratic prioritization

## Related Skills

- For user research methods detail → use `user-research-methods`
- For workshop facilitation → use this skill with `user-research-methods`
- For prototyping tools → use `interaction-design-patterns`
