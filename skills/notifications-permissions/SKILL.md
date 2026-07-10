---
name: notifications-permissions
description: >
  Design notification systems and permission requests that respect users. Covers
  push notification patterns, permission priming, notification center design,
  grouping, frequency management, and the complete permission request flow. Use
  when designing notification systems, permission dialogs, or re-engagement
  strategies. Triggers on: "push notification", "permission dialog", "permission
  priming", "notification center", "re-engagement", "notification design",
  "permission UX".
---

# Notifications & Permissions

Notifications are a privilege, not a right. Abuse them and users disable them permanently — or delete your app.

## Permission Requests

### The Golden Rule
Ask for permission **in context**, immediately after the user experienced the value. Never on first launch.

### The Permission Flow

```
User Action → Soft Prompt (in-app) → System Dialog (OS level)
     ↑              ↑                      ↑
  Context      Explain value          Irreversible (only once)
```

### Soft Prompt Pattern
Before the system dialog (which can only be shown once), use an in-app screen:

**Structure:**
1. **Contextual hook** — "Stay on top of your orders"
2. **Specific benefit** — "Get notified when your package ships and arrives"
3. **Two clear options** — "Enable Notifications" / "Not Now"
4. **Skip if declined** — don't ask again for 30 days

### Timing by Permission Type

| Permission | Best Time to Ask |
|------------|------------------|
| **Push notifications** | After user completes a valuable action (places order, gets a match) |
| **Camera** | When user taps "Take photo" |
| **Location** | When user needs location for a feature (find nearby stores) |
| **Contacts** | When user chooses "Find friends" |
| **Microphone** | When user starts a voice feature |

### If Permission Is Denied
- **Never ask again immediately** — respect the decision
- **Show how to enable in Settings** — if user tries the feature again
- **Provide alternative path** — web upload if camera denied
- **Wait 30+ days before re-prompting** — and only with new context

## Push Notification Design

### Structure

```
┌─────────────────────────────────────┐
│ [App Icon] App Name        2m ago  │
│                                     │
│ Headline (bold, 1 line)            │
│ Body text (2 lines max, specific)  │
│                                     │
│ [Action 1]  [Action 2]  [Dismiss]  │
└─────────────────────────────────────┘
```

### Rules
- **Headline:** Specific, actionable. "Your order shipped" not "Update available"
- **Body:** Personal and relevant. "Package #1234 arrives Tuesday" not "Tap to open"
- **Max 2 action buttons** — primary action + dismiss
- **Deep link to the right screen** — not just the app home

### Notification Categories

| Type | Example | Frequency |
|------|---------|-----------|
| **Transactional** | "Your password was changed" | Event-driven only |
| **Status update** | "Your order shipped" | Per user action |
| **Reminder** | "Your appointment is in 1 hour" | Scheduled, relevant |
| **Re-engagement** | "You have 3 unread messages" | Sparingly |
| **Marketing** | "20% off this weekend" | Rarely, segment-targeted |

### Frequency Guidelines
- **Transactional:** unlimited (user-triggered)
- **Status:** 1-2 per user action lifecycle
- **Re-engagement:** max 1 per day, only if user has pending activity
- **Marketing:** max 1-2 per week, segment-targeted
- **Batch non-urgent** — daily digest instead of individual pings

### Rich Notifications
- **Images** — increase engagement ~20%, but keep under 200KB
- **Action buttons** — let users act without opening the app
- **Grouped/stacked** — combine multiple notifications from same app

## Notification Settings

### Provide Granular Control
Let users choose which types of notifications they receive:

- [ ] Order updates
- [ ] Messages
- [ ] Promotions
- [ ] Account security
- [ ] Daily digest (replaces individual notifications)

### Quiet Hours
Respect local time. Default quiet hours: 10 PM – 8 AM.

## Related Skills

- For onboarding patterns → use `onboarding-ux`
- For UX writing → use `ux-writing-content-design`
- For ethical considerations → use `ethical-design-patterns`
