---
name: calm-technology-ui
description: >
  Amber Case's Calm Technology principles applied to AI interfaces. An 8-point test to determine if your AI UI is calm or clamorous, plus the 3-state AI UI model (Pre-AI / AI-Active / AI-Resolved), ambient activity patterns, and the shimmer/glow design language used by Apple Intelligence. Use when designing AI activity indicators, deciding how to show AI is working, or evaluating whether your interface overwhelms users. Triggers on: "calm technology", "how to show AI working", "AI activity indicator", "shimmer UI", "ambient interface", "Apple Intelligence UI pattern".
---

# Calm Technology for AI UI

Amber Case's 8 principles of Calm Technology, applied to AI-native interfaces. The goal: inform users without demanding attention.

## The 8 Principles

Source: Amber Case, "Calm Technology" (2015); Mark Weiser, Xerox PARC (1991)

1. **Require the least possible attention** — AI works in the background. Users focus on their task, not the AI.
2. **Inform and calm** — AI notifications should inform without interrupting. A subtle pulse, not a popup.
3. **Make use of the periphery** — AI activity belongs at the edge of attention, not the center. Users notice it when needed, ignore it when not.
4. **Amplify the best of technology and humanity** — AI makes humans more capable. It doesn't replace human judgment.
5. **Communicate without speaking** — Visual indicators (glows, badges, color shifts) are often better than text notifications.
6. **Work even when it fails** — If the AI service is down, the app must still work. Graceful degradation is mandatory.
7. **Use the minimum needed** — Don't add AI features just because you can. Every AI feature must earn its place.
8. **Respect social norms** — AI behavior should be predictable. Users should know what to expect.

## The 3-State AI UI Model

| State | Visual | User Experience |
|-------|--------|-----------------|
| **Pre-AI** | No shimmer, clean UI | App works fully without AI |
| **AI-Active** | Shimmer/glow on processing elements | User knows AI is working |
| **AI-Resolved** | Shimmer fades, results appear with subtle badge | User knows AI contributed |

**Critical rule:** The shimmer MUST stop when processing completes. Never leave it as decoration.

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

Key: The shimmer **must stop** when processing completes. Never leave it as decoration.

## Ambient Activity Patterns

| Instead of... | Use... |
|------|-----------|
| Full-screen loading overlay | Subtle glow on the element being worked on |
| Dashboard card with progress bar | Small pulsing dot in the corner |
| "Processing..." spinner for 5s | Number briefly blurs then updates |
| Content pops in with no transition | Result fades in naturally |
| Permanent "AI-generated" watermark | "Done" badge that fades after 2s |

## CSS-Only Ambient Patterns

```css
/* Breathing glow for agent activity */
@keyframes breathe {
  0%, 100% { opacity: 0.4; transform: scale(1); }
  50% { opacity: 1; transform: scale(1.05); }
}
.agent-dot { animation: breathe 2s ease-in-out infinite; }

/* Fade-in for content appearance */
@keyframes fade-in-up {
  from { opacity: 0; transform: translateY(8px); }
  to { opacity: 1; transform: translateY(0); }
}
.content-appear { animation: fade-in-up 0.3s ease-out; }

/* Shimmer loading effect */
@keyframes shimmer {
  0% { background-position: -200% 0; }
  100% { background-position: 200% 0; }
}
.shimmer {
  background: linear-gradient(90deg, #f0f0f0 25%, #e0e0e0 50%, #f0f0f0 75%);
  background-size: 200% 100%;
  animation: shimmer 1.5s infinite;
}
```

## Framer Motion Ambient Patterns

```tsx
// Ambient glow — agent active on element
<motion.div
  animate={{
    boxShadow: isActive
      ? ["0 0 0 rgba(0,0,0,0)", "0 0 20px rgba(59,130,246,0.3)", "0 0 0 rgba(0,0,0,0)"]
      : "0 0 0 rgba(0,0,0,0)"
  }}
  transition={{ duration: 1.8, repeat: Infinity }}
>
  <Content />
</motion.div>

// Image shimmer — designer active
<motion.div
  animate={{
    filter: isActive
      ? ["hue-rotate(0deg)", "hue-rotate(3deg)", "hue-rotate(0deg)"]
      : "hue-rotate(0deg)"
  }}
  transition={{ duration: 2, repeat: Infinity }}
>
  <Image src={creativeImage} />
</motion.div>

// Score counter animation
<motion.span
  key={currentScore}
  initial={{ opacity: 0, y: 10, filter: "blur(4px)" }}
  animate={{ opacity: 1, y: 0, filter: "blur(0px)" }}
  transition={{ duration: 0.3 }}
>
  {currentScore}%
</motion.span>

// Trust overlay — tap and hold
<motion.div
  initial={{ opacity: 0, y: 20 }}
  animate={{ 
    opacity: isHolding ? 1 : 0,
    y: isHolding ? 0 : 20,
    pointerEvents: isHolding ? "auto" : "none"
  }}
  transition={{ duration: 0.2 }}
  className="absolute bottom-0 left-0 right-0 bg-black/90 text-white p-4 rounded-t-lg"
>
  <TrustSummary />
</motion.div>
```

## The Test

Count how many of the 8 principles your interface passes:
- **8 passes:** Exceptionally calm
- **6-7 passes:** Good enough to ship
- **4-5 passes:** Needs work
- **Below 4:** Redesign required

## Related Skills

- For Nielsen's heuristics → use `ai-ui-heuristics`
- For common failure catalog → use `ai-ui-anti-patterns`
- For generative UI patterns → use `ai-native-ui-design`
- For multi-agent UI → use `agentic-ui-patterns`
