# Calm Technology for AI Interfaces

Source: Amber Case, "Calm Technology" (2015); Mark Weiser, Xerox PARC (1991)

## The 8 Principles

1. **Require the least possible attention** — AI works in the background. Users focus on their task.
2. **Inform and calm** — Subtle pulse, not a popup.
3. **Make use of the periphery** — AI activity at the edge of attention, not the center.
4. **Amplify the best of technology and humanity** — AI makes humans more capable.
5. **Communicate without speaking** — Visual indicators (glows, badges) over text notifications.
6. **Work even when it fails** — Graceful degradation is mandatory.
7. **Use the minimum needed** — Every AI feature must earn its place.
8. **Respect social norms** — Predictable behavior.

## The Shimmer Pattern (Apple Intelligence)

```css
@keyframes ai-shimmer {
  0% { background-position: -200% center; }
  100% { background-position: 200% center; }
}
.ai-processing {
  background: linear-gradient(90deg, 
    transparent 0%, rgba(255,255,255,0.1) 50%, transparent 100%);
  background-size: 200% 100%;
  animation: ai-shimmer 1.8s ease-in-out infinite;
}
```

**The shimmer MUST stop when processing completes.**

## The 3 States of AI UI

| State | Visual | Experience |
|-------|--------|------------|
| **Pre-AI** | No shimmer, clean UI | App works fully without AI |
| **AI-Active** | Shimmer/glow on processing elements | User knows AI is working |
| **AI-Resolved** | Shimmer fades, subtle badge appears | User knows AI contributed |

## Calm vs. Clamorous

| Calm | Clamorous |
|------|-----------|
| Subtle glow on element being worked on | Full-screen loading overlay |
| Small pulsing dot in corner | Dashboard card with progress bar |
| Number briefly blurs then updates | "Processing..." spinner for 5s |
| Result fades in naturally | Content pops in with no transition |
| "Done" badge fades after 2s | Permanent "AI-generated" watermark |
