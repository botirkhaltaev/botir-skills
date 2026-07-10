# Nielsen's 10 Heuristics: AI-Native Edition

Source: Jakob Nielsen, Nielsen Norman Group — https://www.nngroup.com/articles/ten-usability-heuristics/

## H1: Visibility of System Status

**AI-specific:** Show when AI is processing, which model is used, confidence level, estimated time.

**Common failure:** User submits prompt, sees spinner for 30s with no idea if AI is working, stuck, or almost done.

**Fix:** Ambient activity indicators. If >5s, show updating messages ("Analyzing...", "Almost there...").

## H2: Match Between System and Real World

**AI-specific:** Natural language prompts, not technical parameters. Name agents by role ("Copywriter" not "LLM-7B"). Avoid AI jargon ("inference", "token", "embedding").

**Common failure:** Interface requires users to know "temperature", "max tokens", "system prompts."

**Fix:** Hide all technical parameters behind "Advanced" toggle. Sensible defaults.

## H3: User Control and Freedom

**AI-specific:** Stop AI mid-generation. Undo every action. Edit outputs inline. Version history.

**Common failure:** AI generates 500 words, user can't stop, can't edit, can't go back.

**Fix:** Stop button. Inline editing. Version history.

## H4: Consistency and Standards

**AI-specific:** Same behavior for same inputs. Predictable output format. Consistent agent names.

**Common failure:** AI gives completely different results for exact same prompt.

**Fix:** Use deterministic parameters (seed, temperature=0). If variation expected, tell user.

## H5: Error Prevention

**AI-specific:** Validate inputs before sending. Show cost estimates. Confirm high-risk actions.

**Common failure:** User accidentally pastes 10,000 words, gets charged $5.

**Fix:** Input length display. Cost estimates. Confirmation for expensive ops.

## H6: Recognition Rather Than Recall

**AI-specific:** Show capabilities as buttons. Example prompts. Recent/favorite prompts.

**Common failure:** User doesn't know what AI can do — no visible cues.

**Fix:** "What can I do?" button with examples. Contextual suggestions.

## H7: Flexibility and Efficiency

**AI-specific:** Keyboard shortcuts. Saved templates. Customizable behaviors. Batch operations.

**Common failure:** Power users click through same flow 50x daily with no shortcuts.

**Fix:** Cmd+K command palette. Saved templates. Batch operations.

## H8: Aesthetic and Minimalist Design

**AI-specific:** No agent status cards. No permanent AI sidebars. One primary action. Output is hero.

**Common failure:** 4 agent cards, 3 progress bars, 2 metric panels, chat sidebar — output in corner.

**Fix:** Output = 80% of screen. Everything else = hover/gesture-revealed.

## H9: Error Recovery

**AI-specific:** Plain language when AI fails. Suggest what to try. Show partial results.

**Common failure:** "An error occurred. Please try again." No indication what went wrong.

**Fix:** "The AI couldn't process your image because it was too large (max 5MB). Try compressing it."

## H10: Help and Documentation

**AI-specific:** In-context help. Example prompts. "How to write good prompts" guide.

**Common failure:** User leaves app, searches Google to figure out AI features.

**Fix:** "Tips" button with example prompts. Contextual hints on hover.
