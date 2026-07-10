---
name: ux-evaluation
description: >
  Frameworks and checklists for evaluating UX quality in AI-native applications. Covers Nielsen's 10 heuristics applied to AI, Don Norman's principles, cognitive load assessment, the calm technology test, and a practical 20-point UX scorecard. Use when reviewing, critiquing, or improving the UX of any AI-powered interface. Triggers on terms like "evaluate UX", "UX review", "critique this UI", "check usability", "UX score", "is this good design", "heuristic evaluation".
---

# UX Evaluation for AI-Native Apps

Evaluate the UX quality of AI-powered interfaces using trusted frameworks and a practical scorecard.

## Quick Scorecard (20 Points)

Rate each item 0-1. A score of 16+ is excellent, 12-15 needs improvement, below 12 needs redesign.

### Visibility & Feedback (4 points)
- [ ] **System status visible** — Users always know when AI is processing, idle, or done
- [ ] **Clear feedback** — Every user action produces visible feedback within 100ms
- [ ] **AI involvement disclosed** — Users can tell which content is AI-generated
- [ ] **Error states clear** — When AI fails, the error message is plain language with a recovery path

### Simplicity & Load (4 points)
- [ ] **Cognitive load low** — No more than 4-7 items visible at once (Miller's Law)
- [ ] **Primary action singular** — One main button per screen, not 3+
- [ ] **Progressive disclosure** — Advanced options hidden behind "more" or revealed on demand
- [ ] **No dashboard fatigue** — Agent activity shown ambiently, not as permanent cards/panels

### Control & Trust (4 points)
- [ ] **Interruptibility** — User can pause/stop AI at any time with one gesture
- [ ] **Undo available** — Every AI action can be undone or rolled back
- [ ] **Confidence shown** — AI outputs display confidence scores where relevant
- [ ] **Kill switch visible** — Emergency stop is accessible within 2 taps

### Output-First Design (4 points)
- [ ] **Output is hero** — Generated content takes 80%+ of the screen
- [ ] **Chrome minimal** — Menus, sidebars, and controls are contextual (hover/click to reveal)
- [ ] **Version comparison easy** — Users can compare AI output versions with ≤2 gestures
- [ ] **Editable output** — Every AI-generated element can be edited inline

### Calm & Accessible (4 points)
- [ ] **Activity ambient** — AI working indicators are subtle (glows/shimmers), not progress bars
- [ ] **No perpetual shimmer** — Activity indicators stop when AI finishes
- [ ] `prefers-reduced-motion` **respected** — Animations disabled for users who need it
- [ ] **Pre-AI state works** — The app is fully usable before any AI activates

## Framework: Nielsen's Heuristics for AI Apps

Nielsen's 10 heuristics, reinterpreted for AI-native interfaces:

| # | Heuristic | AI-Specific Interpretation | Common Failure |
|---|-----------|---------------------------|----------------|
| 1 | **Visibility of status** | Show AI processing state, model used, confidence level | User doesn't know if AI is working or frozen |
| 2 | **Match real world** | Use natural language for prompts, not technical parameters | Users need to learn prompt engineering to use the app |
| 3 | **User control** | Always allow overriding AI decisions, editing outputs | AI output is read-only, can't be changed |
| 4 | **Consistency** | Same AI behavior for same inputs, predictable output format | AI gives inconsistent results for the same prompt |
| 5 | **Error prevention** | Validate inputs before sending to AI, show cost estimates | User accidentally spends $50 on an AI API call |
| 6 | **Recognition > recall** | Show AI capabilities as buttons, not requiring users to remember | Users don't know what the AI can do |
| 7 | **Flexibility** | Keyboard shortcuts, power-user modes, customizable AI behavior | One-size-fits-all AI that can't be tuned |
| 8 | **Minimalist design** | No feature bloat — AI handles complexity behind the scenes | Dashboard with 47 metrics and 12 agent cards |
| 9 | **Error recovery** | Clear error messages when AI fails, with retry/edit options | "Something went wrong" with no recovery path |
| 10 | **Help docs** | In-context help, example prompts, tooltips for AI features | No explanation of what AI features do or how to use them |

## Framework: Don Norman's Principles Applied to AI

Evaluate against Norman's 7 principles:

1. **Discoverability** — Can users figure out what the AI can do without tutorials?
2. **Feedback** — Is there immediate, clear feedback for every AI action?
3. **Conceptual Model** — Do users understand how the AI works (even if simplified)?
4. **Affordances** — Are AI capabilities visible as interactive elements (not hidden)?
5. **Signifiers** — Do visual cues clearly indicate what is AI-generated vs human?
6. **Mapping** — Do controls map naturally to AI actions (e.g., "regenerate this")?
7. **Constraints** — Are dangerous AI actions prevented or gated?

## Framework: Calm Technology Test

From Amber Case's 8 principles — an AI interface should pass at least 6:

| Principle | Test |
|-----------|------|
| **1. Requires least attention** | Can the user focus on their primary task while AI works? |
| **2. Informs without demanding** | Does AI notify without interrupting? |
| **3. Works at periphery** | Is AI activity visible but not central until needed? |
| **4. Amplifies human capability** | Does AI make the user more capable, not replace them? |
| **5. Communicates clearly** | Can users understand what AI did and why? |
| **6. Respects social norms** | Does AI behave predictably in social contexts? |
| **7. Uses minimal resources** | Is the interface lightweight, not resource-heavy? |
| **8. Fails gracefully** | Does the app work normally when AI is unavailable? |

## Framework: Cognitive Load Check

Measure extraneous cognitive load. The interface should not require users to:
- Remember information across screens (violation: recognition > recall)
- Make more than one decision per screen (violation: Hick's Law)
- Process more than 4-7 visible items at once (violation: Miller's Law)
- Learn a new interaction model just for AI features (violation: consistency)

## Practical Evaluation Process

1. **Screenshot the interface** — All screens, all states (idle, loading, success, error)
2. **Run the 20-point scorecard** — Be honest, half points allowed
3. **Check against Nielsen's heuristics** — Note every violation
4. **Apply the calm technology test** — Count how many principles pass
5. **Cognitive load assessment** — Count visible elements on each screen
6. **Prioritize fixes** — Fix scorecard failures first, then heuristic violations

## Common AI UI Failures (Ranked by Severity)

| Rank | Failure | Fix |
|------|---------|-----|
| 1 | User can't tell if AI is working | Add ambient activity indicator |
| 2 | AI output is read-only | Make everything editable inline |
| 3 | No way to stop AI | Add prominent kill switch |
| 4 | Dashboard overwhelms user | Move chrome to hover/gesture |
| 5 | AI failures are hidden | Show errors immediately with recovery |
| 6 | Chat is the only interface | Add buttons, gestures, direct manipulation |
| 7 | No confidence indication | Add per-element confidence scores |
| 8 | Perpetual loading spinners | Stream content, use skeletons |
| 9 | Inconsistent AI behavior | Show which model/settings were used |
| 10 | App breaks without AI | Ensure graceful degradation |

## References

- `references/nielsen-heuristics.md` — Full Nielsen heuristics with AI-specific examples
- `references/calm-tech-test.md` — Amber Case's 8 principles in detail
