---
name: ai-ui-heuristics
description: >
  Nielsen's 10 usability heuristics and Don Norman's 7 principles, reinterpreted for AI-native interfaces. Use when evaluating, auditing, or reviewing the UX of any AI-powered application. Each heuristic includes the AI-specific interpretation, a common failure mode, and a concrete fix. Triggers on: "heuristic evaluation", "Nielsen heuristics", "UX audit", "review this AI UI", "is this good design", "Don Norman principles".
---

# AI UI Heuristics

Nielsen's 10 heuristics and Don Norman's 7 principles, reinterpreted for AI-native interfaces.

## Nielsen's 10 Heuristics for AI

Source: Jakob Nielsen, Nielsen Norman Group — https://www.nngroup.com/articles/ten-usability-heuristics/

### H1: Visibility of System Status
**AI-specific:** Show when AI is processing, which model is used, confidence level, estimated time.
**Common failure:** User submits prompt, sees spinner for 30s with no idea if AI is working, stuck, or almost done.
**Fix:** Ambient activity indicators (subtle glow). If >5s, show updating messages ("Analyzing...", "Almost there...").

### H2: Match Between System and Real World
**AI-specific:** Natural language prompts, not technical parameters. Name agents by role ("Copywriter" not "LLM-7B"). Avoid AI jargon ("inference", "token", "embedding").
**Common failure:** Interface requires users to know about "temperature", "max tokens", or "system prompts."
**Fix:** Hide all technical parameters behind an "Advanced" toggle. Default everything to sensible values.

### H3: User Control and Freedom
**AI-specific:** Stop AI mid-generation. Undo every action. Edit outputs inline. Version history.
**Common failure:** AI generates 500 words and the user can't stop it, can't edit it, and can't go back.
**Fix:** Add a stop button. Make all output editable. Keep version history.

### H4: Consistency and Standards
**AI-specific:** Same AI behavior for same inputs (or explain why different). Predictable output format every time.
**Common failure:** The AI gives completely different results for the exact same prompt, confusing users.
**Fix:** Use deterministic parameters (seed, temperature=0) for consistent outputs. If variation is expected, tell the user.

### H5: Error Prevention
**AI-specific:** Validate inputs before sending to AI. Show cost estimates. Confirm high-risk actions.
**Common failure:** User accidentally pastes a 10,000-word document into a prompt and gets charged $5 for a single request.
**Fix:** Show input length, estimated cost, and a confirmation for expensive operations.

### H6: Recognition Rather Than Recall
**AI-specific:** Show AI capabilities as buttons, not requiring users to remember. Show example prompts for common tasks.
**Common failure:** User doesn't know what the AI can do because there are no visible cues or examples.
**Fix:** Add a "What can I do?" button with example prompts. Include contextual hints that appear on hover.

### H7: Flexibility and Efficiency of Use
**AI-specific:** Keyboard shortcuts for power users. Saved prompt templates. Customizable agent behaviors.
**Common failure:** Power users must click through the same flow 50 times a day with no shortcuts.
**Fix:** Add keyboard shortcuts (Cmd+K for command palette), saved templates, and batch operations.

### H8: Aesthetic and Minimalist Design
**AI-specific:** No dashboard cards showing agent status. No permanent sidebars with AI controls. One primary action per screen. Generated content is the hero.
**Common failure:** Screen has 4 agent cards, 3 progress bars, 2 metric panels, and a chat sidebar — with the actual output squeezed into a corner.
**Fix:** The output takes 80% of the screen. Everything else is hover/gesture-revealed.

### H9: Help Users Recognize, Diagnose, and Recover from Errors
**AI-specific:** When AI fails, explain why in plain language. Suggest what to try next. Show partial results if available.
**Common failure:** "An error occurred. Please try again." — with no indication of what went wrong or how to fix it.
**Fix:** "The AI couldn't process your image because it was too large (max 5MB). Try compressing it or using a smaller image."

### H10: Help and Documentation
**AI-specific:** In-context help (tooltips, hints). Example prompts for every feature. "How to write good prompts" guide accessible from the UI.
**Common failure:** User has to leave the app and search Google to figure out how to use the AI features.
**Fix:** Add a "Tips" button that shows example prompts. Include contextual hints that appear on hover.

## Don Norman's 7 Principles for AI

Source: Don Norman, "The Design of Everyday Things"

1. **Discoverability** — Can users figure out what the AI can do without tutorials?
2. **Feedback** — Is there immediate, clear feedback for every AI action?
3. **Conceptual Model** — Do users understand how the AI works (even if simplified)?
4. **Affordances** — Are AI capabilities visible as interactive elements (not hidden)?
5. **Signifiers** — Do visual cues clearly indicate what is AI-generated vs human?
6. **Mapping** — Do controls map naturally to AI actions (e.g., "regenerate this")?
7. **Constraints** — Are dangerous AI actions prevented or gated?

## Cognitive Load Check

The interface should not require users to:
- Remember information across screens (violation: recognition > recall)
- Make more than one decision per screen (violation: Hick's Law)
- Process more than 4-7 visible items at once (violation: Miller's Law)
- Learn a new interaction model just for AI features (violation: consistency)

## Related Skills

- For calm technology test → use `calm-technology-ui`
- For common failure catalog → use `ai-ui-anti-patterns`
- For generative UI patterns → use `ai-native-ui-design`
- For multi-agent UI → use `agentic-ui-patterns`
