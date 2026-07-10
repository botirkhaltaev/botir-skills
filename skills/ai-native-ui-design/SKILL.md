---
name: ai-native-ui-design
description: >
  Principles and patterns for building AI-native interfaces — where the AI's output IS the primary interface, not a sidebar or chat panel. Covers the generative UI paradigm (Claude Artifacts, ChatGPT Canvas, v0.dev), calm technology for AI, the control transfer loop, ambient activity indicators, and version morphing. This is about the interaction paradigm shift, not visual aesthetics (see anthropics/frontend-design for that) or UI polish (see make-interfaces-feel-better for that). Use when building interfaces for AI-powered apps, agentic tools, or any product where generated content is the hero. Triggers on: "AI UI", "generative UI", "output as interface", "how to show AI output", "Claude Artifacts-style UI", "agentic interface design".
---

# AI-Native UI Design

The AI-native interface paradigm: the generated output IS the primary interface. Not a sidebar. Not a chat panel. Not a dashboard. The creative, the code, the analysis — whatever the AI produces — takes center stage.

## 10 Foundational Principles

1. **Output IS the Interface** — Generated content takes 80%+ of the screen. Chrome is minimal and contextual.
2. **Intent Over Navigation** — Users express what they want; the system decides how to show it.
3. **Stream Everything** — Content never "pops in." Always stream with visible progress. `yield <Loading />` before `return <Content />`.
4. **Calm, Not Clamorous** — AI activity is ambient (subtle glows), never overwhelming (no progress bars, no agent cards). See references/calm-tech.md.
5. **Interruptibility by Default** — Pause, review, redirect. Veto and rollback are primary actions.
6. **Transparency Without Noise** — Show what the AI is doing, but keep it peripheral. Hover for details.
7. **Control Transfer Loop** — AI generates → human edits inline → AI iterates → human approves. The user never loses control.
8. **Design for Failure** — Every AI feature needs 4 states: idle → loading → success → error. Graceful degradation is mandatory.
9. **Progressive Disclosure** — Show the simple prompt input first. Reveal advanced options on demand.
10. **Pre-AI State Must Work** — The interface before AI activates must be a complete, usable design.

## Core Patterns

### Pattern: Generative Canvas

The generated output occupies the full viewport. Controls appear contextually.

```
┌─────────────────────────────────────────────┐
│                                             │
│     [ THE GENERATED OUTPUT IS THE UI ]      │
│                                             │
│     80%+ of screen. Minimal chrome.         │
│     Controls on hover/gesture.              │
│                                             │
├─────────────────────────────────────────────┤
│  ◉ v3  ○ v2  ○ v1  ·  4.1% CTR            │  ← versions + one metric
│  [      Approve & Deploy      ]             │  ← ONE primary action
└─────────────────────────────────────────────┘
```

**Do:** Make AI-generated content the hero. One primary action. Dots for versions.
**Don't:** Surround output with agent cards, progress bars, dashboards. Multiple primary buttons.

### Pattern: Ambient Activity

Show AI is working without visual noise.

| Instead of... | Use... |
|---------------|--------|
| Progress bars | Subtle glow on the element being worked on |
| "Agent working..." text | Element shimmers (hue-rotate, opacity pulse) |
| Dashboard cards | Corner pulsing dots |
| Full-screen loaders | Skeleton → content morph |

```css
/* Ambient glow — Apple Intelligence style */
@keyframes ai-glow {
  0%, 100% { text-shadow: 0 0 0 transparent; }
  50% { text-shadow: 0 0 20px rgba(251, 191, 36, 0.25); }
}
.ai-active { animation: ai-glow 1.8s ease-in-out infinite; }
```

**Critical:** The glow MUST stop when the agent finishes. Never leave it as decoration.

### Pattern: Control Transfer Loop

The defining interaction for AI-native apps:

```
1. AI GENERATES → full output appears (user watches)
2. HUMAN EDITS → user modifies inline (click to edit)
3. AI ITERATES → agent revises based on feedback (streams update)
4. HUMAN APPROVES → one tap to finalize
```

**Do:** Make every element editable. Show "AI edited this" badges that fade after 2s. Allow "regenerate this section" on any element.
**Don't:** Make output read-only. Force users into chat to make changes.

### Pattern: Version Morphing

Compare versions through gesture, not tabs.

**Implementation (Framer Motion):**
```tsx
<AnimatePresence mode="wait" custom={direction}>
  <motion.div
    key={version.id}
    custom={direction}
    initial={(d) => ({ opacity: 0, x: d * 50 })}
    animate={{ opacity: 1, x: 0 }}
    exit={(d) => ({ opacity: 0, x: d * -50 })}
    transition={{ type: "spring", stiffness: 300, damping: 30 }}
  >
    <GeneratedOutput data={version} />
  </motion.div>
</AnimatePresence>
```

**Do:** Swipe left/right to compare. Score animates as you swipe. Show context: "v1: 1.2% → v2: 2.8% → v3: 4.1%"
**Don't:** Use tabs or dropdowns for version switching.

### Pattern: Contextual Trust

Safety info appears when needed, not as a permanent panel.

| Gesture | What Appears |
|---------|-------------|
| Hover element | "Confidence: 94% · 3 iterations · AI-generated" |
| Tap and hold | Trust overlay: agents, audit trail, kill switch |
| Swipe up | Minimal trust panel (one glance, dismissible) |

## streamUI Code Pattern (Vercel AI SDK)

Generator functions with `yield` for loading states:

```typescript
import { streamUI } from 'ai';

async function generateUI(prompt: string) {
  const result = await streamUI({
    model: openai('gpt-4o'),
    prompt,
    tools: {
      generateCreative: {
        description: 'Generate an ad creative',
        parameters: z.object({ headline: z.string() }),
        generate: async function* ({ headline }) {
          yield <SkeletonCreative />;  // loading state first
          const creative = await createCreative(headline);
          return <CreativeDisplay data={creative} />;  // final content
        },
      },
    },
  });
  return result.value;
}
```

## Anti-Patterns

| Pattern | Why It Fails | Better Approach |
|---------|-------------|----------------|
| **Chatbot Trap** | Chat is the wrong interface for many tasks | Use buttons, direct manipulation, inline editing |
| **Dashboard Fatigue** | Agent cards + progress bars overwhelm | Ambient indicators, output-first layout |
| **Hidden AI** | Users don't know AI was involved | Subtle "AI-generated" badges that build trust |
| **Over-Decoration** | Shimmer/glow becomes permanent style | Activity indicators MUST stop when done |
| **No Fallback** | App is useless without AI | Pre-AI state must be a complete product |

## Relationship to Other Skills

- **For visual aesthetics** (typography, color, layout) → use `anthropics/frontend-design`
- **For UI polish** (border radius, shadows, micro-interactions) → use `jakubkrehel/make-interfaces-feel-better`
- **For showing multiple agents working** → use `agentic-ui-patterns`
- **For evaluating UX quality** → use `ux-audit-ai-apps`

## References

- `references/calm-tech.md` — Amber Case's 8 principles applied to AI UI
- `references/generative-ui-examples.md` — How Claude Artifacts, Canvas, v0, Cursor do it
- `references/animation-patterns.md` — Framer Motion + CSS patterns with code
