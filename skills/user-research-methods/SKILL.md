---
name: user-research-methods
description: >
  Practical UX research methods: when to use which method, how to execute it,
  and how to synthesize findings into actionable insights. Covers qualitative
  methods (interviews, usability testing, field studies), quantitative methods
  (surveys, A/B tests, analytics), and mixed methods. Use when planning
  research, choosing methods, or synthesizing findings. Triggers on:
  "user research", "usability testing", "user interviews", "survey design",
  "research synthesis", "empathy map", "persona", "journey mapping".
---

# User Research Methods

Research is not a phase — it's a continuous activity that informs every design decision. Pick the right method for the question you have.

## Method Selection Framework

| Question | Method | Effort |
|----------|--------|--------|
| What do users think/feel? | User interviews | Medium |
| Can users complete a task? | Usability testing | Medium |
| How many users have this problem? | Survey + analytics | Low |
| What do users do naturally? | Field study / contextual inquiry | High |
| Which design performs better? | A/B testing | Medium |
| How do users navigate? | Tree testing | Low |
| What groups of users exist? | Card sorting + segmentation | Medium |
| What is the full experience? | Journey mapping | Medium |

## Qualitative Methods

### User Interviews
One-on-one conversations to understand attitudes, motivations, and mental models.

**When to use:** Early discovery, understanding "why", exploring new domains
**Sample size:** 5-8 participants per segment
**Duration:** 30-60 minutes

#### Best Practices
- **Ask open-ended questions** — "Tell me about the last time you..." not "Do you like..."
- **Follow the 5 Whys** — dig deeper on interesting answers
- **Silence is OK** — let participants think
- **Never ask leading questions** — "Don't you think..." biases answers
- **Record with permission** — you can't take notes and listen fully

### Usability Testing
Observing users attempt tasks with a prototype or product.

**When to use:** Evaluating designs, finding usability issues, validating flows
**Sample size:** 5 participants uncovers ~85% of issues (Nielsen)
**Duration:** 30-45 minutes, 3-5 tasks

#### Structure
1. **Welcome** (2 min) — build rapport, set expectations
2. **Context questions** (5 min) — understand their background
3. **Tasks** (20-30 min) — observe without helping
4. **Debrief** (5 min) — ask about overall impressions

#### Key Rules
- **Test the product, not the person** — make this explicit
- **Think-aloud protocol** — ask users to verbalize their thoughts
- **Don't help** — watching them struggle is the point
- **Note: task success, time, errors, satisfaction**

### Field Studies / Contextual Inquiry
Observing users in their natural environment.

**When to use:** Understanding real-world constraints, complex workflows, B2B tools
**Sample size:** 4-6 site visits
**Duration:** 2-4 hours per visit

**Master/apprentice model:** You are the apprentice, they are the master. Ask them to teach you their work.

## Quantitative Methods

### Surveys
Collecting structured data from many users.

**When to use:** Validating hypotheses, measuring attitudes, segmenting users
**Sample size:** 30+ for directional, 200+ for statistical significance

#### Question Types
| Type | Use For | Example |
|------|---------|---------|
| **Likert scale** | Measuring agreement | "Strongly disagree" to "Strongly agree" (5 or 7 point) |
| **Multiple choice** | Categorizing | "What is your role?" |
| **Ranking** | Prioritizing | "Rank these features by importance" |
| **Open text** | Capturing nuance | "What would make this better?" |
| **NPS** | Loyalty metric | "How likely are you to recommend... (0-10)" |

#### Survey Best Practices
- **Keep under 10 minutes** — completion drops sharply after
- **One question per screen** on mobile
- **Randomize answer order** (except Likert scales)
- **Required questions minimum** — every required field reduces completion
- **Test with 5 people** before sending broadly

### A/B Testing
Comparing two variants to measure which performs better.

**When to use:** Optimizing existing flows, validating design decisions at scale
**Sample size:** Depends on baseline rate and minimum detectable effect (use a calculator)

#### Rules
- **Test one variable at a time** (or use multivariate testing)
- **Run until statistical significance** (p < 0.05)
- **Define success metric before starting** — primary metric + guardrail metrics
- **Run for complete business cycles** (full weeks, not partial)

## Synthesis Methods

### Affinity Mapping
Grouping observations into themes.

1. Write each observation on a sticky note
2. Group related notes into clusters
3. Name each cluster with a theme
4. Identify patterns and insights across clusters

### Empathy Map
Capture what users say, think, do, and feel.

```
        ┌─────────────┬─────────────┐
        │    Says     │    Thinks   │
        │  (quotes)   │  (inferred) │
        ├─────────────┼─────────────┤
        │     Does    │    Feels    │
        │  (observed) │  (emotions) │
        └─────────────┴─────────────┘
```

### User Persona
A fictional but realistic representation of a user segment. Not a demographic sketch — a behavioral and motivational profile.

Include: goals, frustrations, behaviors, tools they use, context of use.
Avoid: stock photos, fake names that distract, demographic data that doesn't affect design.

### Journey Map
Visualize the end-to-end experience across touchpoints.

| Stage | Action | Thought | Feeling | Touchpoint | Pain Point | Opportunity |
|-------|--------|---------|---------|------------|------------|-------------|
| Aware | Sees ad | "What's this?" | Curious | Instagram | — | Better targeting |
| Sign up | Creates account | "Hope this is quick" | Anxious | Landing page | Form too long | Social login |
| First use | Uploads file | "Did it work?" | Uncertain | Dashboard | No confirmation | Success animation |

## Research Artifacts Hierarchy

```
Raw Data → Synthesis → Insights → Recommendations

Notes      Affinity     Themes     Action items
Quotes     map          Insights   with priority
Videos     Personas     "Users     and owner
Analytics  Journey      struggle   ──→
           maps         with X"
```

## Related Skills

- For design thinking process → use `design-thinking-process`
- For UX writing research → use `ux-writing-content-design`
- For accessibility research → use `a11y-accessibility-design`
