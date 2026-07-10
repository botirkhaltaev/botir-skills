---
name: ux-audit-ai-apps
description: >
  Evaluate the UX quality of AI-powered applications using established frameworks: Nielsen's 10 heuristics (AI-native edition), Don Norman's principles, the Calm Technology test, and a practical 20-point scorecard. This evaluates UX DESIGN quality, not code compliance (see vercel-labs/web-design-guidelines for code auditing) or visual aesthetics (see anthropics/frontend-design or make-interfaces-feel-better for design implementation). Use when reviewing, critiquing, or improving the UX of any AI-powered interface. Triggers on: "evaluate UX", "UX review", "critique this UI", "check usability", "UX score", "heuristic evaluation", "is this good design", "audit this AI app".
---

# UX Audit for AI Apps

Evaluate UX quality of AI-powered interfaces using established frameworks, adapted for the unique challenges of AI-native apps.

## Quick Scorecard (20 Points)

Rate 0-1 each. 16+ = excellent, 12-15 = needs work, below 12 = redesign.

### Visibility & Feedback (4 points)
- [ ] **System status visible** — Users always know when AI is processing, idle, or done
- [ ] **Clear feedback** — Every action produces visible feedback within 100ms
- [ ] **AI involvement disclosed** — Users can tell which content is AI-generated
- [ ] **Error states clear** — Plain language error messages with recovery paths

### Simplicity & Load (4 points)
- [ ] **Cognitive load low** — ≤7 items visible at once (Miller's Law)
- [ ] **Primary action singular** — One main button per screen
- [ ] **Progressive disclosure** — Advanced options hidden until needed
- [ ] **No dashboard fatigue** — Agent activity shown ambiently, not as cards

### Control & Trust (4 points)
- [ ] **Interruptibility** — Pause/stop AI at any time with one gesture
- [ ] **Undo available** — Every AI action can be undone
- [ ] **Confidence shown** — AI outputs display confidence scores
- [ ] **Kill switch visible** — Emergency stop within 2 taps

### Output-First Design (4 points)
- [ ] **Output is hero** — Generated content takes 80%+ of screen
- [ ] **Chrome minimal** — Controls contextual (hover/click to reveal)
- [ ] **Version comparison easy** — Compare versions with ≤2 gestures
- [ ] **Editable output** — Every AI-generated element editable inline

### Calm & Accessible (4 points)
- [ ] **Activity ambient** — Indicators are subtle (glows), not progress bars
- [ ] **No perpetual shimmer** — Indicators stop when AI finishes
- [ ] `prefers-reduced-motion` **respected** — Animations disabled when needed
- [ ] **Pre-AI state works** — App is fully usable before any AI activates

## Nielsen's Heuristics: AI-Native Edition

| # | Heuristic | AI-Specific Interpretation | Common Failure |
|---|-----------|---------------------------|----------------|
| 1 | **Visibility of status** | Show AI processing state, model used, confidence | User doesn't know if AI is working or frozen |
| 2 | **Match real world** | Natural language prompts, not technical parameters | Users need to learn prompt engineering |
| 3 | **User control** | Override AI decisions, edit outputs inline | AI output is read-only |
| 4 | **Consistency** | Same behavior for same inputs | AI gives different results for same prompt |
| 5 | **Error prevention** | Validate inputs, show cost estimates | Accidental expensive API calls |
| 6 | **Recognition > recall** | Show capabilities as buttons, not hidden | Users don't know what AI can do |
| 7 | **Flexibility** | Keyboard shortcuts, customizable behavior | One-size-fits-all AI |
| 8 | **Minimalist design** | AI handles complexity behind the scenes | Dashboard with 47 metrics |
| 9 | **Error recovery** | Plain language errors with retry paths | "Something went wrong" |
| 10 | **Help docs** | In-context help, example prompts | No explanation of AI features |

## Don Norman's Principles for AI

1. **Discoverability** — Can users figure out what the AI can do without tutorials?
2. **Feedback** — Immediate, clear feedback for every AI action?
3. **Conceptual Model** — Do users understand how the AI works?
4. **Affordances** — Are AI capabilities visible as interactive elements?
5. **Signifiers** — Do visual cues indicate AI-generated vs human content?
6. **Mapping** — Do controls map naturally to AI actions?
7. **Constraints** — Are dangerous AI actions prevented or gated?

## Calm Technology Test (Amber Case)

An AI interface should pass at least 6 of 8:

| # | Principle | Test |
|---|-----------|------|
| 1 | Requires least attention | Can user focus on their task while AI works? |
| 2 | Informs without demanding | Does AI notify without interrupting? |
| 3 | Works at periphery | Is AI activity visible but not central? |
| 4 | Amplifies human capability | Does AI make the user more capable? |
| 5 | Communicates clearly | Can users understand what AI did and why? |
| 6 | Respects social norms | Is AI behavior predictable? |
| 7 | Uses minimal resources | Is the interface lightweight? |
| 8 | Fails gracefully | Does the app work when AI is unavailable? |

## Cognitive Load Check

The interface should not require users to:
- Remember information across screens (violation: recognition > recall)
- Make more than one decision per screen (violation: Hick's Law)
- Process more than 4-7 visible items at once (violation: Miller's Law)
- Learn a new interaction model just for AI features (violation: consistency)

## Common AI UI Failures (Ranked)

| Rank | Failure | Fix |
|------|---------|-----|
| 1 | User can't tell if AI is working | Ambient activity indicator |
| 2 | AI output is read-only | Inline editing |
| 3 | No way to stop AI | Prominent kill switch |
| 4 | Dashboard overwhelms | Move chrome to hover/gesture |
| 5 | AI failures hidden | Show errors + recovery |
| 6 | Chat is the only interface | Buttons, gestures, direct manipulation |
| 7 | No confidence indication | Per-element confidence scores |
| 8 | Perpetual loading spinners | Stream content, use skeletons |
| 9 | Inconsistent AI behavior | Show model/settings used |
| 10 | App breaks without AI | Graceful degradation |

## Audit Process

1. Screenshot all screens, all states (idle, loading, success, error)
2. Run the 20-point scorecard
3. Check against Nielsen's heuristics — note every violation
4. Apply the Calm Technology test — count passes
5. Cognitive load assessment — count visible elements per screen
6. Prioritize: fix scorecard failures first, then heuristic violations

## Relationship to Other Skills

- **For code compliance audit** → use `vercel-labs/web-design-guidelines`
- **For visual design quality** → use `anthropics/frontend-design`
- **For UI polish details** → use `jakubkrehel/make-interfaces-feel-better`
- **For AI-native UI patterns** → use `ai-native-ui-design`
- **For multi-agent UI patterns** → use `agentic-ui-patterns`

## References

- `references/nielsen-heuristics.md` — Full heuristics with AI-specific examples
- `references/calm-tech-test.md` — Detailed Calm Technology principles
