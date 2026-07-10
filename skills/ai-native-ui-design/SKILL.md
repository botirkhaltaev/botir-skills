---
name: ai-native-ui-design
description: >
  Core principles and patterns for building AI-native user interfaces where the AI output IS the interface — not a sidebar or chat panel. Covers generative UI, calm technology, output-as-interface paradigm, ambient activity indicators, and the 10 foundational principles distilled from Vercel AI SDK, Claude Artifacts, ChatGPT Canvas, Apple Intelligence, and leading AI products. Use when designing or building interfaces for AI-powered applications, agentic apps, or generative products. Triggers on terms like "AI UI", "generative UI", "agentic interface", "AI-native design", "how to show AI output".
---

# AI-Native UI Design

Design interfaces where the AI's output IS the primary interface — not a sidebar, chat panel, or dashboard bolted onto traditional software.

## The 10 Foundational Principles

1. **Output IS the Interface** — The generated content takes 80%+ of the screen. Chrome (menus, sidebars, controls) is minimal and contextual.
2. **Intent Over Navigation** — Users express what they want; the system decides how to show it. No complex menus or navigation hierarchies.
3. **Stream Everything** — Content never "pops in." Always stream with visible progress. Use `yield <Loading />` before `return <Component />`.
4. **Calm, Not Clamorous** — AI activity is ambient (subtle glows, shimmers), never overwhelming (no progress bars, no dashboard cards). See references/calm-tech.md.
5. **Interruptibility by Default** — Users can always pause, review, and redirect. Pause/veto/rollback are primary actions, not edge cases.
6. **Transparency Without Noise** — Show what the AI is doing and why, but keep it peripheral. Hover/click for details, don't dedicate screen real estate.
7. **Control Transfer, Not Handoff** — AI generates → human edits → AI iterates. The user never loses control. Always allow inline editing.
8. **Design for Failure** — Every AI feature needs all 4 states: idle → loading → success → error. Graceful degradation is mandatory.
9. **Progressive Disclosure** — Show the simple prompt input first, reveal advanced options on demand. Never overwhelm with all capabilities at once.
10. **Pre-AI State Must Work** — The interface before AI activates must be a complete, usable design. Not a degraded "you're missing out" experience.

## Core Patterns

### Pattern: Generative Canvas
The generated output occupies the full viewport. Controls appear contextually.

```
┌─────────────────────────────────────────────┐
│                                             │
│     [ THE GENERATED OUTPUT IS THE UI ]      │
│                                             │
│     Takes 80%+ of the screen.               │
│     Minimal chrome.                         │
│     Controls on hover/gesture.              │
│                                             │
├─────────────────────────────────────────────┤
│  ◉ v3  ○ v2  ○ v1  ·  4.1% CTR            │  ← versions + one metric
│  [      Approve & Deploy      ]             │  ← ONE primary action
│                                             │
└─────────────────────────────────────────────┘
```

**What to do:**
- Make the AI-generated content the hero element
- Put controls on hover, not permanently visible
- Show ONE primary action, not 3+ buttons
- Use dots/pills for version switching, not tabs

**What NOT to do:**
- Don't surround the output with agent cards, progress bars, and dashboards
- Don't have multiple primary buttons (Deploy, Iterate, Export, Share)
- Don't use traditional sidebar + top-nav + content layout

### Pattern: Ambient Activity
Show AI is working without visual noise.

| Instead of... | Use... |
|---------------|--------|
| Progress bars | Subtle glow on the element being worked on |
| "Agent working..." labels | Element shimmers (hue-rotate, opacity pulse) |
| Dashboard cards | Corner pulsing dots per agent |
| Full-screen loaders | Skeleton → content morph |

**CSS for ambient glow (Apple Intelligence-style):**
```css
@keyframes ai-glow {
  0%, 100% { text-shadow: 0 0 0 transparent; }
  50% { text-shadow: 0 0 20px rgba(251, 191, 36, 0.25); }
}
.ai-active { animation: ai-glow 1.8s ease-in-out infinite; }
```

### Pattern: Control Transfer Loop
The defining interaction pattern for AI-native apps:

```
1. AI GENERATES → full output appears (user watches)
2. HUMAN EDITS → user modifies inline (click to edit)
3. AI ITERATES → agent revises based on feedback (streams update)
4. HUMAN APPROVES → one tap to finalize
```

**What to do:**
- Make every output element editable by default
- Show "AI edited this" badges that fade after 2 seconds
- Allow "regenerate this section" on any element (right-click or long-press)
- Stream updates in real-time, never show "loading..." spinners

### Pattern: Version Morphing
Let users compare versions through gesture, not tabs.

```
Swipe left/right → creative morphs between versions
Score animates    → CTR counts up/down as you swipe
Context shown     → "v1: 1.2% → v2: 2.8% → v3: 4.1%"
```

**Implementation (Framer Motion):**
```tsx
<AnimatePresence mode="wait">
  <motion.div
    key={version.id}
    initial={{ opacity: 0, scale: 0.95, x: 50 }}
    animate={{ opacity: 1, scale: 1, x: 0 }}
    exit={{ opacity: 0, scale: 1.05, x: -50 }}
    transition={{ type: "spring", stiffness: 300, damping: 30 }}
  >
    <GeneratedOutput data={version} />
  </motion.div>
</AnimatePresence>
```

### Pattern: Contextual Trust
Safety info appears when needed, not as a permanent panel.

| Gesture | What Appears |
|---------|-------------|
| Hover an element | "Confidence: 94% · 3 iterations · AI-generated" |
| Tap and hold | Trust overlay: agents, audit trail, kill switch |
| Swipe up | Minimal trust panel (one glance, dismissible) |

## The streamUI Pattern (Vercel AI SDK)

Use generator functions with `yield` for loading states:

```typescript
import { streamUI } from 'ai';

export async function generateUI(prompt: string) {
  const result = await streamUI({
    model: openai('gpt-4o'),
    prompt,
    text: ({ content }) => <div>{content}</div>,
    tools: {
      generateCreative: {
        description: 'Generate an ad creative',
        parameters: z.object({ headline: z.string(), image: z.string() }),
        generate: async function* ({ headline, image }) {
          yield <SkeletonCreative />;  // loading state
          const creative = await generateCreative(headline, image);
          return <CreativeDisplay data={creative} />;  // final
        },
      },
    },
  });
  return result.value;
}
```

## Anti-Patterns (What to Avoid)

- **The Chatbot Trap** — Chat is often the wrong interface. Don't add conversation where a button suffices.
- **Dashboard Fatigue** — Don't show agent cards with progress bars. The output IS the progress.
- **Hidden AI** — Don't hide that AI is involved. Subtle badges build trust.
- **Over-Decoration** — Shimmer/glow should signal activity, not be a permanent style.
- **No Fallback** — Always work without AI. The pre-AI state must be a complete product.

## References

- `references/calm-tech.md` — Amber Case's 8 principles for calm technology, applied to AI
- `references/generative-ui-examples.md` — How Claude Artifacts, ChatGPT Canvas, v0, and Cursor implement generative UI
- `references/animation-patterns.md` — Specific Framer Motion and CSS patterns for AI UI
