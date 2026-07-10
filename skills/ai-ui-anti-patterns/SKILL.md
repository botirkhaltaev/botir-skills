---
name: ai-ui-anti-patterns
description: >
  The 10 most common AI UI failures, ranked by severity, with root cause analysis and concrete fixes. Covers the chatbot trap, dashboard fatigue, perpetual shimmer, hidden AI activity, read-only output, missing kill switches, and more. Use when debugging AI interface problems, reviewing an AI app for UX issues, or preventing common mistakes in AI-native design. Triggers on: "AI UI mistakes", "common AI UI failures", "anti-patterns", "what not to do", "AI UI pitfalls", "fix this AI interface".
---

# AI UI Anti-Patterns

The 10 most common failures in AI-native interface design, ranked by severity. Each includes the root cause, the user impact, and a concrete fix.

## #1: User Can't Tell If AI Is Working
**Root cause:** No visible system status. The AI is processing but there's no indicator.
**User impact:** User thinks the app is frozen. They refresh, resubmit, or abandon.
**Fix:** Add ambient activity indicator — subtle glow on the element being worked on, or a small pulsing dot in the corner. See `calm-technology-ui` for patterns.

## #2: AI Output Is Read-Only
**Root cause:** Generated content is rendered as static text/images, not editable elements.
**User impact:** User sees a mistake but can't fix it inline. They must regenerate entirely or copy-paste to another tool.
**Fix:** Make every AI-generated element editable by default. Click to edit. Show "regenerate this section" on any element.

## #3: No Way to Stop AI
**Root cause:** No stop/cancel mechanism. Once started, the AI runs to completion.
**User impact:** User watches helplessly as AI generates wrong content, wasting time and tokens.
**Fix:** Prominent stop button. Kill switch within 2 taps. Scoped control — stop individual agents, not just everything.

## #4: Dashboard Overwhelms User
**Root cause:** Too much chrome — agent cards, progress bars, metric panels, sidebars — squeezing the actual output into a corner.
**User impact:** Cognitive overload. User can't focus on the content because they're monitoring 12 UI elements.
**Fix:** Output takes 80% of screen. Move chrome to hover/gesture. One primary action. See `ai-native-ui-design` for the generative canvas pattern.

## #5: AI Failures Are Hidden
**Root cause:** Errors are logged to console or hidden in settings. User sees nothing wrong but gets bad output.
**User impact:** User trusts bad output. Mistakes propagate downstream.
**Fix:** Show errors immediately in plain language with recovery path. "The AI couldn't process your image because it was too large (max 5MB). Try compressing it."

## #6: Chat Is the Only Interface
**Root cause:** The "chatbot trap" — every interaction goes through a chat conversation.
**User impact:** Slow, tedious interaction. Users must type what they want instead of clicking or gesturing.
**Fix:** Use buttons for common actions. Direct manipulation for editing. Chat for open-ended requests only. See `ai-native-ui-design` for the control transfer loop pattern.

## #7: No Confidence Indication
**Root cause:** AI outputs are presented as fact with no indication of reliability.
**User impact:** User can't distinguish high-confidence output from speculative guesswork.
**Fix:** Per-element confidence scores. Inline badges: "Your headline here" [94%]. Hover → "3 iterations, strong test results."

## #8: Perpetual Loading Spinners
**Root cause:** Using generic spinners for all loading states instead of streaming content.
**User impact:** User stares at a spinner for 30 seconds with no sense of progress.
**Fix:** Stream content as it's generated. Use skeleton → content morph. Show partial results immediately.

## #9: Inconsistent AI Behavior
**Root cause:** Same prompt produces wildly different results. No explanation of why.
**User impact:** User can't build mental model of what the AI can and can't do. Trust erodes.
**Fix:** Show which model and settings were used. Use deterministic parameters when consistency matters. If variation is expected, tell the user.

## #10: App Breaks Without AI
**Root cause:** The interface is a shell that only works when AI is available.
**User impact:** App becomes completely unusable when AI service is down or user is offline.
**Fix:** Graceful degradation. The pre-AI state must be a complete, usable product. Every feature should have a manual fallback.

## Quick Checklist

Before shipping an AI feature, verify:
- [ ] User can tell when AI is working (ambient indicator)
- [ ] User can edit any AI-generated output inline
- [ ] User can stop AI at any time (kill switch within 2 taps)
- [ ] Generated content is the hero (80%+ of screen)
- [ ] Errors are visible immediately with recovery path
- [ ] Common actions have buttons, not just chat
- [ ] Confidence scores shown on AI output
- [ ] Content streams in real-time, not spinner
- [ ] AI behavior is consistent or variation is explained
- [ ] App works fully when AI is unavailable

## Related Skills

- For calm technology principles → use `calm-technology-ui`
- For Nielsen's heuristics → use `ai-ui-heuristics`
- For generative UI patterns → use `ai-native-ui-design`
- For multi-agent UI → use `agentic-ui-patterns`
