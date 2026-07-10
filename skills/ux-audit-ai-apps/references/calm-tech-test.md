# Calm Technology Test (Amber Case)

Source: Amber Case, "Calm Technology" (2015)

## The 8 Principles (Detailed)

### 1. Technology should require the least possible amount of attention

**Test:** Can the user focus on their primary task while AI works in the background?

**Pass example:** AI generates a report while the user continues reading another document.
**Fail example:** User must watch a loading screen for 30 seconds, unable to do anything else.

### 2. Technology should inform and calm

**Test:** Does AI notify without interrupting?

**Pass example:** A subtle dot pulses in the corner when AI is done.
**Fail example:** A popup appears: "AI has finished generating your report!" blocking the screen.

### 3. Technology should make use of the periphery

**Test:** Is AI activity visible but not central until needed?

**Pass example:** Small status indicator at the edge of the screen.
**Fail example:** Full-screen dashboard showing every agent's status in detail.

### 4. Technology should amplify the best of technology and the best of humanity

**Test:** Does AI make the user more capable, not replace them?

**Pass example:** AI drafts an email; user edits and sends.
**Fail example:** AI sends emails automatically without user review.

### 5. Technology should communicate, but doesn't need to speak

**Test:** Can users understand AI state without reading text?

**Pass example:** Color shift, glow, or animation conveys state.
**Fail example:** Status messages: "Processing step 3 of 7..."

### 6. Technology should work even when it fails

**Test:** Does the app work normally when AI is unavailable?

**Pass example:** App falls back to manual mode; all features still accessible.
**Fail example:** App shows "AI service unavailable" and becomes unusable.

### 7. The right amount of technology is the minimum needed to solve the problem

**Test:** Is every AI feature justified?

**Pass example:** AI only assists where it genuinely helps.
**Fail example:** AI added to every feature "because we can."

### 8. Technology should respect social norms

**Test:** Is AI behavior predictable?

**Pass example:** AI behaves consistently; users learn what to expect.
**Fail example:** AI surprises users with unexpected actions.

## Scoring

Count how many principles pass. Target: **6+ to ship**, 5 = needs work, below 5 = redesign.
