# Nielsen's 10 Heuristics: AI-Native Edition

Source: Jakob Nielsen, Nielsen Norman Group — https://www.nngroup.com/articles/ten-usability-heuristics/

## Heuristic #1: Visibility of System Status

**Core:** The design should always keep users informed about what is going on.

**AI-specific:**
- Show when AI is processing (ambient shimmer/glow)
- Show which model is being used
- Show estimated time remaining (or "a few seconds")
- Show when AI has completed and what it did

**Common failure:** User submits a prompt, sees a spinner for 30 seconds with no idea if the AI is working, stuck, or almost done.

**Fix:** Add ambient activity indicators. If processing takes >5 seconds, show a progress message like "Analyzing your request..." that updates every few seconds.

---

## Heuristic #2: Match Between System and Real World

**Core:** Speak the users' language. Use familiar concepts.

**AI-specific:**
- Use natural language for prompts, not technical parameters
- Name agents by their role ("Copywriter" not "LLM-7B-GPT-v3")
- Use real-world metaphors ("Factory floor", "Assembly line")
- Avoid AI jargon in the UI ("inference", "token", "embedding")

**Common failure:** Interface requires users to know about "temperature", "max tokens", or "system prompts" to get good results.

**Fix:** Hide all technical parameters behind an "Advanced" toggle. Default everything to sensible values.

---

## Heuristic #3: User Control and Freedom

**Core:** Users need clearly marked "emergency exits."

**AI-specific:**
- Always allow stopping AI mid-generation
- Support undo for every AI action
- Allow editing AI-generated output inline
- Provide "back to previous version" for iterations

**Common failure:** AI generates 500 words and the user can't stop it, can't edit it, and can't go back.

**Fix:** Add a stop button, make all output editable, and keep version history.

---

## Heuristic #4: Consistency and Standards

**Core:** Follow platform conventions.

**AI-specific:**
- Same AI behavior for the same inputs (or explain why different)
- Predictable output format every time
- Consistent agent names and roles across sessions
- Follow platform conventions (iOS HIG, Material Design)

**Common failure:** The AI gives completely different results for the exact same prompt, confusing users.

**Fix:** Use deterministic parameters (seed, temperature=0) for consistent outputs. If variation is expected, tell the user.

---

## Heuristic #5: Error Prevention

**Core:** Prevent problems from occurring.

**AI-specific:**
- Validate inputs before sending to AI (check length, format)
- Show cost estimates before expensive operations
- Confirm high-risk actions (deploy, send, delete)
- Set sensible defaults that prevent bad outcomes

**Common failure:** User accidentally pastes a 10,000-word document into a prompt and gets charged $5 for a single request.

**Fix:** Show input length, estimated cost, and a confirmation for expensive operations.

---

## Heuristic #6: Recognition Rather Than Recall

**Core:** Make objects, actions, and options visible.

**AI-specific:**
- Show AI capabilities as buttons, not requiring users to remember
- Show example prompts for common tasks
- Show recent/favorite prompts for reuse
- Display agent capabilities in the UI, not in docs

**Common failure:** User doesn't know what the AI can do because there are no visible cues or examples.

**Fix:** Add a "What can I do?" button with example prompts. Show suggested actions contextually.

---

## Heuristic #7: Flexibility and Efficiency of Use

**Core:** Allow users to tailor frequent actions.

**AI-specific:**
- Keyboard shortcuts for power users
- Saved prompt templates
- Customizable agent behaviors
- Batch operations for repetitive tasks

**Common failure:** Power users must click through the same flow 50 times a day with no shortcuts.

**Fix:** Add keyboard shortcuts (Cmd+K for command palette), saved templates, and batch operations.

---

## Heuristic #8: Aesthetic and Minimalist Design

**Core:** Interfaces should not contain information that is irrelevant.

**AI-specific:**
- No dashboard cards showing agent status (ambient indicators instead)
- No permanent sidebars with AI controls (contextual instead)
- One primary action per screen
- Generated content is the hero, not the controls

**Common failure:** Screen has 4 agent cards, 3 progress bars, 2 metric panels, and a chat sidebar — with the actual output squeezed into a corner.

**Fix:** The output takes 80% of the screen. Everything else is hover/gesture-revealed.

---

## Heuristic #9: Help Users Recognize, Diagnose, and Recover from Errors

**Core:** Error messages should be in plain language.

**AI-specific:**
- When AI fails, explain why in plain language
- Suggest what to try next
- Show partial results if available (don't discard everything)
- Log errors for debugging without exposing them to users

**Common failure:** "An error occurred. Please try again." — with no indication of what went wrong or how to fix it.

**Fix:** "The AI couldn't process your image because it was too large (max 5MB). Try compressing it or using a smaller image."

---

## Heuristic #10: Help and Documentation

**Core:** Documentation should be easy to search.

**AI-specific:**
- In-context help (tooltips, hints)
- Example prompts for every feature
- "How to write good prompts" guide accessible from the UI
- Video demos for complex features

**Common failure:** User has to leave the app and search Google to figure out how to use the AI features.

**Fix:** Add a "Tips" button that shows example prompts. Include contextual hints that appear on hover.
