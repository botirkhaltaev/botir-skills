# Multi-Agent UI Examples from Leading Products

## Cursor (Multiple Agents Window)

**What they do:** Show agent thinking + file-by-file progress in a sidebar.

**Pattern:**
- Agent's reasoning visible as it plans
- Each file change streams in real-time
- Terminal output visible for verification
- User can interrupt with follow-up at any time

**UI Structure:**
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

**Key insight:** Transparency of agent thinking builds trust. Seeing the plan before execution lets users course-correct.

## Replit Agent 4 (Task Board)

**What they do:** Trello-style board showing parallel agent tasks.

**Pattern:**
- Tasks in columns: Drafts → Active → Ready → Done
- Each card shows: agent name, task description, progress
- Design previews appear as agents work
- Package installs and tests visible in real-time

**UI Structure:**
```
┌──────────┬──────────┬──────────┬──────────┐
│  Drafts  │  Active  │  Ready   │   Done   │
├──────────┼──────────┼──────────┼──────────┤
│ Setup DB │Create    │Design    │Install   │
│          │auth flow │preview   │packages  │
│          │  ●●●     │  ●●●●    │  ●●●●●   │
└──────────┴──────────┴──────────┴──────────┘
```

**Key insight:** Task-based visualization makes parallel work comprehensible. Users understand the pipeline at a glance.

## Zed (Thread-Based Collaboration)

**What they do:** Threads sidebar for multi-agent collaboration.

**Pattern:**
- Each agent conversation is a thread
- Threads can reference each other
- Agent outputs are contextually linked
- Human can jump between threads

**UI Structure:**
```
┌──────────┬───────────────────────┐
│ Threads  │ Editor                │
│          │                       │
│ ▼ Agent 1│ // Generated code     │
│   "Set   │ // from Agent 1       │
│    up..."│                       │
│          │                       │
│ ▶ Agent 2│                       │
│   "Style │                       │
│    the..."                       │
└──────────┴───────────────────────┘
```

**Key insight:** Thread-based organization scales to many agents without overwhelming the user.

## What Works Across All Products

1. **Real-time visibility** — Users see progress as it happens
2. **Interruptibility** — Stop/redirect at any moment
3. **Task granularity** — Each agent has a clear, named task
4. **Status clarity** — Working/Done/Waiting states are obvious
5. **Output focus** — The generated artifact is always prominent

## What Doesn't Work

- **Full dashboards as primary UI** — Users want to see the output, not the factory
- **Permanent agent cards** — Status should be ambient, not always visible
- **Hidden parallelization** — Users should know multiple agents are working
- **No failure visibility** — When an agent fails, it must be obvious
