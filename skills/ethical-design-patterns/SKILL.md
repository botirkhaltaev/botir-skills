---
name: ethical-design-patterns
description: >
  Recognize dark patterns and design with integrity. Covers the taxonomy of
  deceptive patterns, ethical frameworks, the regulatory landscape, and
  practical guidelines for ethical persuasion. Use when auditing interfaces
  for manipulation, establishing ethical design standards, or evaluating
  growth tactics. Triggers on: "dark patterns", "ethical design", "deceptive
  patterns", "ethical UX", "manipulative design", "persuasive design",
  "design ethics", "anti-patterns ethics".
---

# Ethical Design Patterns

Recognize manipulation and design with integrity. Ethical design builds trust; dark patterns destroy it.

> "Dark patterns are not bad design. They are design that works exactly as intended — against the user." — Harry Brignull

## Dark Patterns Taxonomy

### 1. Roach Motel
Easy to get in, hard to get out.

**Examples:** Subscriptions that are easy to start but require calling customer service to cancel. Accounts that can be created in one click but deletion requires emailing support.

**Fix:** Make exit as easy as entry. One-click cancellation. Self-service account deletion.

### 2. Sneak into Basket
Items added without explicit user consent.

**Examples:** Insurance pre-selected at checkout. Donation added without clear opt-in. "Premium" version auto-selected.

**Fix:** Nothing is added the user didn't explicitly select. Default to the base option.

### 3. Confirmshaming
Guilt-tripping users into opting in.

**Examples:** "No, I don't like saving money" / "No, I prefer to miss out."

**Fix:** Neutral language for both options. "Subscribe" / "No thanks" — not guilt-laden.

### 4. Forced Continuity
Auto-renewing subscriptions without adequate reminder.

**Examples:** Free trial converts to paid without clear warning. Annual subscription renews without email notice.

**Fix:** Send reminder 7 days before renewal. Make billing frequency clear at signup. Easy cancellation before renewal.

### 5. Misdirection
Visual design nudges users toward the company's preferred action.

**Examples:** Primary button styled as a ghost button. "Save" is small gray text; "Discard" is large green. Opt-out buried under expandable sections.

**Fix:** Visual hierarchy matches user intent, not business intent. Important user actions (cancel, delete, opt-out) get equal or greater visual weight.

### 6. Interface Interference
Making undesirable actions difficult through UI design.

**Examples:** Tiny gray "unsubscribe" link at the bottom. "Delete account" requires navigating 4 menus. Toggle switches with reversed logic.

**Fix:** All actions a user might reasonably want are findable within 2 clicks. Destructive actions and opt-outs are as visible as positive actions.

### 7. Nagging
Repeated interruptions until the user gives in.

**Examples:** Rate-this-app popup on every launch. Push notifications every day until enabled. Email opt-in modal on every page visit.

**Fix:** Ask once. If declined, respect it. Provide a "Don't ask again" option.

### 8. False Urgency
Creating artificial scarcity or time pressure.

**Examples:** "Only 2 left!" (when inventory is actually high). Countdown timer that resets. "Sale ends tonight" that's perpetual.

**Fix:** Only show genuine scarcity. If the timer resets, don't use a timer.

## Ethical Design Framework

### The Ethical Hierarchy of Persuasion

| Level | Approach | Example |
|-------|----------|---------|
| **Enable** | Help users do what they already want | Smart defaults, clear paths |
| **Educate** | Inform users to make better decisions | Nutrition labels, cost breakdowns |
| **Encourage** | Nudge toward beneficial behavior | "You saved $10 by choosing annual" |
| **Discourage** | Make harmful choices slightly harder | Extra confirmation for deletion |
| **Restrict** | Prevent genuinely harmful actions | Spending limits, safety locks |

**Rules:**
- Enable and Educate are always ethical
- Encourage is ethical if it benefits the user
- Discourage is ethical for truly destructive actions
- Restrict is ethical only for safety-critical contexts

### The Test: Would You Explain This to a User?

If you had to explain this design choice to a user face-to-face, would you be embarrassed? That's your answer.

### Questions to Ask
1. Does this respect user autonomy?
2. Is the user's interest aligned with the business interest?
3. Would a reasonable user feel deceived?
4. Is the default choice the safest/least committal option?
5. Can users easily undo this action?

## Regulatory Landscape

| Region | Regulation | What It Covers |
|--------|------------|----------------|
| **EU** | GDPR | Consent must be freely given, specific, informed, unambiguous |
| **EU** | Digital Services Act | Dark patterns in online interfaces are prohibited |
| **USA** | FTC Act | Deceptive practices are unlawful (Section 5) |
| **USA (state)** | California AADC | Restricts dark patterns for minors |
| **India** | CPA Guidelines | Bans dark patterns in consumer protection |

## Ethical Persuasion Techniques (The Good Kind)

| Technique | How | Example |
|-----------|-----|---------|
| **Smart defaults** | Pre-select the option most users want | Annual billing selected (but monthly visible) |
| **Social proof** | Show what others do (truthfully) | "Join 10,000+ teams" |
| **Progressive disclosure** | Show complexity on demand | "Advanced options" toggle |
| **Friction for the right things** | Add confirmation for irreversible actions | "This deletes all data. Type DELETE to confirm." |
| **Transparent pricing** | All costs visible before commitment | Show total with tax before checkout |

## Related Skills

- For notifications and permissions → use `notifications-permissions`
- For onboarding ethics → use `onboarding-ux`
- For accessibility and inclusive design → use `a11y-accessibility-design`
