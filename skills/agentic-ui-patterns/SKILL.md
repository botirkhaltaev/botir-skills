---
name: agentic-ui-patterns
description: >
  UI patterns for multi-agent applications — how to show multiple AI agents working together without overwhelming the user. Covers agent orchestration UI, ambient activity indicators, the control transfer loop, trust dashboards, kill switches, and confidence scoring. Distilled from Cursor, Replit Agent, Claude, and leading agentic products. Use when building apps with multiple AI agents, software factories, or any system where agents work autonomously. Triggers on terms like "multi-agent UI", "show agents working", "agent dashboard", "software factory UI", "orchestrate agents", "agentic interface".
---

# Agentic UI Patterns

UI patterns for applications where multiple AI agents work autonomously — showing their activity, building trust, and keeping the user in control without overwhelming them.

## The Core Challenge

> "Users do not know what the agent is doing, why it made a choice, or how to stop it. That opacity kills trust. Lost trust kills adoption." — YUJ Designs

The goal: make agent activity **visible but not noisy**, **trustworthy but not bureaucratic**.

## Pattern: Agent Activity Map

Show what agents are doing without dedicating screen space to status cards.

### Minimal: Corner Dots
```
┌──────────────────────────────────────────────┐
│                                              │
│     [ Main Generated Output Here ]           │
│                                              │
│                                              │
├──────────────────────────────────────────────┤
│  ◉ ○ ○ ○  ← 4 agents, 1 active             │  ← 4 tiny dots, bottom-left
│  [ Approve ]                                 │
└──────────────────────────────────────────────┘
```

- Each dot = one agent
- Pulsing dot = currently active
- Tap dots → expand to agent names + what they're doing
- Dots collapse back to minimal after 2 seconds of inactivity

### Expanded: Agent Ribbon
When user taps the dots, a minimal ribbon expands:
```
Copywriter   ● Done      "3 headlines generated"
Designer     ○ Working   "Creating visual concept..."
Analyst      ● Done      "Forecast: $170K revenue"
Tester       ○ Waiting   "Waiting for creative..."
```

**Rules:**
- Ribbon auto-collapses after 3 seconds of no interaction
- Never show more than 4 agents without grouping
- Status words: "Working" | "Done" | "Waiting" — never "Processing" or "Executing"

### Ambient: Output-Bound Indicators
Attach agent activity to the specific part of the output they're working on:

| Agent | Indicator Location | Visual |
|-------|-------------------|--------|
| Copywriter | On the headline text | Subtle amber text glow |
| Designer | On the image | Soft hue-rotate shimmer |
| Media Buyer | Platform targeting | Small FB/IG icon pulses in corner |
| Analyst | Price, metrics | Numbers blur then sharpen |

**CSS for headline glow:**
```css
@keyframes copywriter-glow {
  0%, 100% { text-shadow: 0 0 0 transparent; }
  50% { text-shadow: 0 0 16px rgba(251, 191, 36, 0.2); }
}
.agent-copywriter-active { animation: copywriter-glow 1.8s ease-in-out infinite; }
```

## Pattern: The Iteration Loop UI

Show the feedback loop: create → test → fail → fix → pass.

### Compact Iteration Trail
Show iteration history in a single line:
```
v1: 1.2% CTR ❌ → v2: 2.8% CTR ⚠️ → v3: 4.1% CTR ✅
```

Tap any version → see exactly what changed:
```
v2 changes:
  • Headline: "Eco bottle" → "Your coffee got greener" (+134%)
  • Image: product shot → lifestyle photo (+46%)
  • Total: 1.2% → 2.8%
```

### Visual Iteration Graph
For complex iterations, show a tiny sparkline:
```
CTR:  1.2% ──╮  2.8% ──╮  4.1%
      ❌      │  ⚠️      │  ✅
      v1     │  v2      │  v3
             └──────────┘
              Copywriter  Designer
              revised     revised
              headline    image
```

## Pattern: Trust Dashboard (Minimal)

A single-screen overlay with everything the user needs to trust the system.

### Structure
```
┌─────────────────────────────────────────────┐
│  🛡️  Trust Summary                         │
├─────────────────────────────────────────────┤
│                                             │
│  Agents: 4 active on Cursor Cloud VMs      │
│  • Copywriter (GPT-5.5)      ● Online      │
│  • Designer (Composer-2)     ● Online      │
│  • Analyst (GPT-5.5)         ● Online      │
│  • Tester (Claude)           ○ Idle        │
│                                             │
│  This session:                              │
│  • 3 iterations performed                   │
│  • 12 agent actions logged                  │
│  • 0 human overrides used                   │
│                                             │
│  Confidence: 94% (high)                     │
│  ━━━━━━━━━━━━━━━━━━━━━━━━                   │
│                                             │
│  [ 🛑 Halt All Agents ]  [ View Audit ]     │
│                                             │
└─────────────────────────────────────────────┘
```

**Rules:**
- ONE screen. No tabs. No scrolling.
- Kill switch is prominent (red, always visible)
- Audit trail accessible but not shown by default
- Confidence score is a single number with a label (not a complex chart)

### Kill Switch Design

The kill switch must be:
- **Visible** — not buried in menus
- **Scoped** — "Stop Copywriter" not just "Stop Everything"
- **Immediate** — no confirmation dialog for emergency stop
- **Recoverable** — show "Agents halted. [Resume]" after stopping

```
┌─────────────────────────────────────────────┐
│  🛑  AGENTS HALTED                          │
│  All 4 agents stopped at your request.      │
│                                             │
│  [ Resume Agents ]  [ Review Changes ]      │
└─────────────────────────────────────────────┘
```

## Pattern: Confidence Scoring

Show how much the user should trust each output.

### Score Display
```
Confidence: ████████████████████░░  94%
```

### Score Ranges
| Score | Label | Meaning | UI Treatment |
|-------|-------|---------|--------------|
| 90-100% | High | Trust this output | Green dot, no warnings |
| 70-89% | Medium | Review recommended | Yellow dot, subtle warning |
| 50-69% | Low | Human review required | Orange dot, clear warning |
| 0-49% | Uncertain | Don't use without revision | Red dot, prominent warning |

### Per-Element Scores
Attach confidence to individual output elements, not just the whole:
```
"Your morning coffee just got 10x greener" [94%]
                                           ↑ hover → "3 iterations, strong test results"
```

## Pattern: Approval Gates

Friction proportional to consequence:

| Risk Level | Example | Friction |
|-----------|---------|----------|
| **Low** | Draft email, internal doc | None — auto-approve |
| **Medium** | Customer-facing copy, ad creative | One-tap approve |
| **High** | Production deploy, financial doc | Tap + Face ID/PIN + confirmation |

### Approval UI
```
┌─────────────────────────────────────────────┐
│  ⚡  High-Risk Action                        │
│  Deploy to 50,000 users?                    │
│                                             │
│  [    Cancel    ]  [  Approve with PIN  ]   │
└─────────────────────────────────────────────┘
```

## Pattern: Multi-Agent Orchestration UI

When 4+ agents work in parallel, show the factory floor without the factory noise.

### Compact: Status Line
```
4 agents working · 2 done · 1 active · 1 waiting
```

### Expanded: Factory Floor (for demos)
For hackathon demos or power-user views, show the full factory:
```
[COPYWRITER]  →  [DESIGNER]  →  [ANALYST]
   📝 Done          🎨 Active      📊 Waiting
                   (VM #2)        (for input)
```

**Rules:**
- Only show this in "power user" or demo mode
- Default UI uses corner dots
- Factory floor auto-hides after agent completion
- Each agent card: name, icon, status (Done/Working/Waiting), VM ID

## Pattern: The Return Moment

When users come back after hours, show a smart briefing:
```
┌─────────────────────────────────────────────┐
│  📋  While you were away                     │
│                                             │
│  ✅ 3 campaigns completed                    │
│  ⚠️ 1 needs your approval                    │
│  ❌ 1 failed (budget exceeded)               │
│                                             │
│  [ Review Now ]  [ Dismiss ]                │
└─────────────────────────────────────────────┘
```

## Anti-Patterns

- **Agent Cards as Primary UI** — Don't make agent status cards the main interface. The output is the interface.
- **Perpetual Shimmer** — Glow effects must stop when agents finish. Never leave shimmer as decoration.
- **Hidden Failures** — If an agent fails, show it immediately. Don't hide errors in logs.
- **All-Or-Nothing Approval** — Don't require approving the entire output. Allow per-element approval.
- **No Scoped Control** — Always allow stopping individual agents, not just "stop everything."

## References

- `references/multi-agent-examples.md` — How Cursor, Replit, Zed, and Claude show multi-agent UIs
- `references/trust-patterns.md` — Kill switch design, confidence scoring, audit trails
