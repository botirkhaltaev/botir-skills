---
name: agentic-ui-patterns
description: >
  UI patterns for applications with multiple autonomous AI agents working together. Covers agent activity indicators (ambient, not dashboard-based), trust dashboards, kill switches, confidence scoring, approval gates, and iteration loop visualization. This is about orchestrating agents in the UI, not about general UI polish (see make-interfaces-feel-better) or visual design (see frontend-design). Use when building software factories, multi-agent systems, or any app where 2+ agents work autonomously. Triggers on: "multi-agent UI", "show agents working", "agent dashboard", "software factory UI", "agent orchestration interface", "how to display AI agents".
---

# Agentic UI Patterns

UI patterns for applications where multiple AI agents work autonomously. The challenge: make agent activity visible but not noisy, trustworthy but not bureaucratic.

> "Users do not know what the agent is doing, why it made a choice, or how to stop it. That opacity kills trust. Lost trust kills adoption." — YUJ Designs

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

- Each dot = one agent. Pulsing = currently active.
- Tap dots → expand to agent names + what they're doing.
- Auto-collapse after 2 seconds of inactivity.

### Expanded: Agent Ribbon
```
Copywriter   ● Done      "3 headlines generated"
Designer     ○ Working   "Creating visual concept..."
Analyst      ● Done      "Forecast: $170K revenue"
Tester       ○ Waiting   "Waiting for creative..."
```

- Auto-collapses after 3 seconds.
- Status words: "Working" | "Done" | "Waiting" — never "Processing" or "Executing".
- Never show more than 4 agents without grouping.

### Ambient: Output-Bound Indicators
Attach agent activity to the specific part of the output they're working on:

| Agent | What They Touch | Visual |
|-------|----------------|--------|
| Copywriter | Headline text | Subtle amber text glow |
| Designer | Product image | Soft hue-rotate shimmer |
| Analyst | Metrics/numbers | Numbers blur then sharpen |
| Tester | Score/badge | Score pulses while calculating |

```css
/* Headline glow when copywriter is active */
@keyframes copywriter-glow {
  0%, 100% { text-shadow: 0 0 0 transparent; }
  50% { text-shadow: 0 0 16px rgba(251, 191, 36, 0.2); }
}
.agent-copywriter-active { animation: copywriter-glow 1.8s ease-in-out infinite; }
```

## Pattern: The Iteration Loop

Show the feedback loop: create → test → fail → fix → pass.

### Compact Iteration Trail
```
v1: 1.2% CTR ❌ → v2: 2.8% CTR ⚠️ → v3: 4.1% CTR ✅
```

Tap any version → see exactly what changed:
```
v2 changes:
  • Headline: "Eco bottle" → "Your coffee got greener" (+134%)
  • Image: product shot → lifestyle photo (+46%)
```

## Pattern: Trust Dashboard (Minimal)

Single-screen overlay. No tabs. No scrolling.

```
┌─────────────────────────────────────────────┐
│  🛡️  Trust Summary                         │
├─────────────────────────────────────────────┤
│  Agents: 4 active on cloud VMs             │
│  • Copywriter (GPT-5.5)      ● Online      │
│  • Designer (Composer-2)     ● Online      │
│  • Analyst (GPT-5.5)         ● Online      │
│  • Tester (Claude)           ○ Idle        │
│                                             │
│  This session: 3 iterations, 12 actions     │
│  Confidence: 94% (high)                     │
│  ━━━━━━━━━━━━━━━━━━━━━━━━                   │
│  [ 🛑 Halt All Agents ]  [ View Audit ]     │
└─────────────────────────────────────────────┘
```

**Access:** Tap and hold the creative → trust overlay fades in. Release → fades out.

## Pattern: Kill Switch

- **Visible** — Within 2 taps, not buried in menus.
- **Scoped** — "Stop Copywriter" not just "Stop Everything".
- **Immediate** — No confirmation dialog.
- **Recoverable** — Show "Agents halted. [Resume]" after stopping.

```tsx
function KillSwitch({ agents, onHalt, onResume }) {
  const [halted, setHalted] = useState(false);
  
  if (halted) return (
    <div className="bg-red-50 border border-red-200 rounded-lg p-4">
      <div className="flex items-center gap-2 text-red-700">
        <AlertOctagon size={16} />
        <span className="font-medium">AGENTS HALTED</span>
      </div>
      <p className="text-sm text-red-600 mt-1">{agents.length} agents stopped</p>
      <div className="flex gap-2 mt-3">
        <Button onClick={onResume} variant="outline">Resume</Button>
        <Button onClick={onReview}>Review Changes</Button>
      </div>
    </div>
  );
  
  return (
    <button onClick={() => { onHalt(); setHalted(true); }}
      className="flex items-center gap-1.5 text-red-500 hover:text-red-700 
                 hover:bg-red-50 px-3 py-1.5 rounded-md transition-colors">
      <StopCircle size={14} />
      <span className="text-sm">Halt Agents</span>
    </button>
  );
}
```

## Pattern: Confidence Scoring

### Score Ranges
| Score | Label | UI Treatment |
|-------|-------|--------------|
| 90-100% | High | Green dot, no warnings |
| 70-89% | Medium | Yellow dot, subtle warning |
| 50-69% | Low | Orange dot, clear warning |
| 0-49% | Uncertain | Red dot, prominent warning |

### Per-Element Scores
```
"Your morning coffee just got 10x greener" [94%]
                                        ↑ hover → "3 iterations, strong results"
```

## Pattern: Approval Gates

Friction proportional to consequence:

| Risk | Example | Friction |
|------|---------|----------|
| **Low** | Draft email | None — auto-approve |
| **Medium** | Customer-facing copy | One-tap approve |
| **High** | Production deploy | Tap + Face ID + confirmation |

## Pattern: Multi-Agent Orchestration (Demo Mode)

For hackathon demos or power-user views, show the factory floor:

```
[COPYWRITER]  →  [DESIGNER]  →  [ANALYST]
   📝 Done          🎨 Active      📊 Waiting
                   (VM #2)        (for input)
```

**Rules:** Only in "power user" or demo mode. Default UI uses corner dots. Auto-hides after completion.

## Anti-Patterns

- **Agent Cards as Primary UI** — The output is the interface, not agent status.
- **Perpetual Shimmer** — Glow effects must stop when agents finish.
- **Hidden Failures** — If an agent fails, show it immediately.
- **All-Or-Nothing Approval** — Allow per-element approval.
- **No Scoped Control** — Always allow stopping individual agents.

## References

- `references/multi-agent-examples.md` — Cursor, Replit, Zed, Claude patterns
- `references/trust-patterns.md` — Kill switch, confidence scoring, audit trails
