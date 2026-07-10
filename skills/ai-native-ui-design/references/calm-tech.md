# Calm Technology Principles for AI Interfaces

Source: Amber Case, "Calm Technology" (2015); Mark Weiser, Xerox PARC (1991)

## The 8 Principles

1. **Technology should require the least possible amount of attention** — AI should work in the background. Users focus on their task, not the AI.

2. **Technology should inform and calm** — AI notifications should inform without interrupting. A subtle pulse, not a popup.

3. **Technology should make use of the periphery** — AI activity belongs at the edge of attention, not the center. Users notice it when needed, ignore it when not.

4. **Technology should amplify the best of technology and the best of humanity** — AI makes humans more capable. It doesn't replace human judgment.

5. **Technology can communicate, but doesn't need to speak** — Visual indicators (glows, badges, color shifts) are often better than text notifications.

6. **Technology should work even when it fails** — If the AI service is down, the app must still work. Graceful degradation is mandatory.

7. **The right amount of technology is the minimum needed to solve the problem** — Don't add AI features just because you can. Every AI feature must earn its place.

8. **Technology should respect social norms** — AI behavior should be predictable. Users should know what to expect.

## Application to AI UI

### The Shimmer Pattern (Apple Intelligence)

Apple's AI uses a rotating gradient shimmer to indicate active processing:

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

Key: The shimmer **must stop** when processing completes. Never leave it as decoration.

### The 3 States of AI UI (Apple Model)

| State | Visual | User Experience |
|-------|--------|-----------------|
| **Pre-AI** | No shimmer, clean UI | App works fully without AI |
| **AI-Active** | Shimmer/glow on processing elements | User knows AI is working |
| **AI-Resolved** | Shimmer fades, results appear with subtle badge | User knows AI contributed |

### Calm vs. Clamorous Checklist

| Calm | Clamorous |
|------|-----------|
| Subtle glow on the element being worked on | Full-screen loading overlay |
| Small pulsing dot in the corner | Dashboard card with progress bar |
| Number briefly blurs then updates | "Processing..." spinner for 5 seconds |
| Result fades in naturally | Content pops in with no transition |
| "Done" badge that fades after 2s | Permanent "AI-generated" watermark |
