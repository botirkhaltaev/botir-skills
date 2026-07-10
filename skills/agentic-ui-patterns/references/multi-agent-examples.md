# Multi-Agent UI Examples from Leading Products

## Cursor (Multiple Agents Window)

**What they do:** Show agent thinking + file-by-file progress in sidebar.

- Agent's reasoning visible as it plans
- Each file change streams in real-time
- Terminal output visible for verification
- User can interrupt with follow-up at any time

**Structure:**
```
┌─────────────────┬───────────────────────┐
│ Agent Chat      │ Code Editor           │
│                 │                       │
│ "I'll create    │ file1.ts ████████ 100%│
│  a React..."    │ file2.ts ████░░░░ 50% │
│                 │                       │
│ [Stop] [Retry]  │ [Accept] [Reject]     │
└─────────────────┴───────────────────────┘
```

**Key insight:** Transparency of agent thinking builds trust.

## Replit Agent 4 (Task Board)

**What they do:** Trello-style board showing parallel agent tasks.

- Tasks in columns: Drafts → Active → Ready → Done
- Each card: agent name, task, progress
- Design previews appear as agents work

**Structure:**
```
┌──────────┬──────────┬──────────┬──────────┐
│  Drafts  │  Active  │  Ready   │   Done   │
├──────────┼──────────┼──────────┼──────────┤
│ Setup DB │Create    │Design    │Install   │
│          │auth flow │preview   │packages  │
└──────────┴──────────┴──────────┴──────────┘
```

**Key insight:** Task-based visualization makes parallel work comprehensible.

## Zed (Thread-Based)

**What they do:** Threads sidebar for multi-agent collaboration.

- Each agent conversation is a thread
- Threads can reference each other
- Human can jump between threads

**Key insight:** Thread-based organization scales to many agents without overwhelming.

## What Works Across All

1. **Real-time visibility** — Progress as it happens
2. **Interruptibility** — Stop/redirect at any moment
3. **Task granularity** — Each agent has a clear, named task
4. **Status clarity** — Working/Done/Waiting states are obvious
5. **Output focus** — The generated artifact is always prominent

## What Doesn't Work

- Full dashboards as primary UI
- Permanent agent cards
- Hidden parallelization
- No failure visibility
