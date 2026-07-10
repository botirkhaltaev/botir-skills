---
name: ux-writing-content-design
description: >
  Write interface copy that clarifies, guides, and converts. Covers microcopy,
  tone of voice, error messages, form labels, empty states, onboarding copy,
  and content design principles. Use when writing or reviewing any text that
  appears in an interface. Triggers on: "UX writing", "microcopy", "error
  message", "tone of voice", "content design", "interface copy", "UX copy",
  "button label", "form label".
---

# UX Writing & Content Design

Every word in an interface is a design decision. UX writing is not copywriting — it's about clarity over cleverness, action over description.

> "The words in an interface are not decoration. They are functional. They clarify, they guide, they reduce friction." — Torrey Podmajersky

## Core Principles

### 1. Clear, Not Clever
Users don't read interfaces — they scan. Every word must earn its place.

| Instead of... | Use... | Why |
|---------------|--------|-----|
| "Utilize" | "Use" | Shorter, more common |
| "In order to" | "To" | Remove filler words |
| "Please be advised that" | (delete) | Adds nothing |
| "We are unable to process your request at this time" | "Something went wrong. Try again." | Plain language |

### 2. Concise
Remove words that don't change the meaning.

| Instead of... | Use... |
|---------------|--------|
| "Click the button below in order to proceed with the registration process" | "Register" |
| "Would you like to save your changes before exiting?" | "Save changes before leaving?" |
| "Your password must contain at least 8 characters" | "Password: 8+ characters" |

### 3. Conversational
Write like a helpful human, not a system.

| Instead of... | Use... |
|---------------|--------|
| "Invalid credentials" | "Wrong email or password" |
| "Operation completed successfully" | "Done" |
| "Error 402: PaymentException" | "Payment failed. Try a different card." |

### 4. Action-Oriented
Lead with the verb. Tell users what to do.

| Instead of... | Use... |
|---------------|--------|
| "Subscription management" | "Manage subscription" |
| "Your account settings" | "Update account" |
| "More information" | "Learn more" |

### 5. User-Centric
Frame from the user's perspective, not the company's.

| Instead of... | Use... |
|---------------|--------|
| "We need your email" | "What's your email?" |
| "We have updated our terms" | "Our terms changed. Here's what matters." |
| "Company policy requires verification" | "Verify your account to continue" |

## Tone of Voice

### Define Your Voice Dimensions
Pick a position on each axis:

| Dimension | This end | ... | ... | That end |
|-----------|----------|-----|-----|----------|
| **Serious ↔ Playful** | Formal, reserved | → | → | Casual, fun |
| **Enthusiastic ↔ Matter-of-fact** | Excited, energetic | → | → | Neutral, calm |
| **Respectful ↔ Irreverent** | Polite, deferential | → | → | Bold, cheeky |

Example: A fintech app might be **matter-of-fact + respectful** ("Your transfer was sent"). A social app might be **playful + enthusiastic** ("You're all set! 🎉").

### Tone Adjusts by Context
The same voice speaks differently in different situations:

| Context | Tone | Example |
|---------|------|---------|
| Onboarding | Welcoming, encouraging | "Let's get you set up" |
| Success | Positive, brief | "Done" / "Saved" |
| Error | Helpful, blame-free | "Something went wrong on our end" |
| Confirmation | Clear, serious | "This will delete all your data. This cannot be undone." |
| Empty state | Guiding, motivating | "No projects yet. Create your first one." |

## Error Messages

### The 3-Part Structure
Every error message needs all three:

1. **What happened** — plain language
2. **Why it happened** — be specific
3. **How to fix it** — actionable next step

| Bad | Good |
|-----|------|
| "Error 404" | "We can't find that page. Try searching or go home." |
| "Payment failed" | "Your card was declined. Try a different card or contact your bank." |
| "Invalid input" | "Email is required. Enter the email you signed up with." |

### Error Message Rules
- **Never blame the user** — "Something went wrong on our end" not "You did something wrong"
- **Avoid "invalid"** — it means nothing. Be specific about what's wrong.
- **Avoid "please" in errors** — it softens urgency. "Please try again" → "Try again"
- **One error, one action** — give exactly one clear next step

## Button Labels

### Rules
- **Start with a verb** — "Save", "Continue", "Delete", "Send"
- **Be specific** — "Send message" not "Submit"
- **No "OK"** — "OK" is lazy. Use the action name: "Got it", "Dismiss", "Save"
- **Pair clearly** — "Cancel" / "Delete" (not "Cancel" / "OK" for destructive actions)

### Destructive Action Patterns
| Primary Action | Secondary Action |
|----------------|------------------|
| Delete account | Keep account |
| Discard changes | Keep editing |
| Leave group | Stay |

## Form Labels & Instructions

### Labels
- **Above the input** — fastest scanning (vs. left-aligned or placeholder)
- **Sentence case** — "Email address" not "Email Address"
- **Required field indicator** — mark required, not optional. Asterisk (*) is standard.

### Placeholder Text
- **Never use as a label** — it disappears when typed
- **Use as an example** — placeholder="you@company.com"
- **Keep it short** — 2-3 words max

### Helper Text
- **Below the input** — "Must be at least 8 characters"
- **Only when needed** — don't state the obvious

## Related Skills

- For error state patterns → use `interaction-design-patterns`
- For empty state patterns → use `interaction-design-patterns`
- For onboarding patterns → use `onboarding-ux`
- For accessibility and plain language → use `a11y-accessibility-design`
