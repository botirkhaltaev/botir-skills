# Generative UI: How Leading Products Do It

## Claude Artifacts (Anthropic)

**Pattern:** The artifact IS the workspace. Not a panel. Not a sidebar. The primary interface element.

- Artifacts appear inline in the conversation but expand to a full workspace
- Runtime sandbox — code executes live, not just displayed
- Users can edit artifacts directly (control transfer loop)
- MCP integration lets artifacts use external tools
- Version history with diff view

**Key insight:** The artifact replaces the chat as the primary interface. Chat becomes secondary.

## ChatGPT Canvas (OpenAI)

**Pattern:** Persistent side panel + inline editing + version history.

- Canvas opens as a side panel for focused editing
- Highlight any text to get AI suggestions inline
- Version history with one-click restore
- One-click shortcuts ("Make it longer", "Change tone")
- Copilot-style inline suggestions (ghost text)

**Key insight:** Canvas creates a dedicated editing space without leaving the conversation. The best of both worlds.

## Cursor Agent UI

**Pattern:** Agent thinking visible + code changes stream in real-time.

- Agent's "thinking" is visible as it plans the approach
- Code changes stream file-by-file, not all at once
- User can interrupt at any point with follow-up instructions
- Diff view shows exactly what changed
- Terminal output streams live for verification

**Key insight:** Transparency builds trust. Watching the agent think and act makes users comfortable.

## Replit Agent 4

**Pattern:** Trello-style progress board + design-while-building.

- Parallel agents shown as task cards (Drafts → Active → Ready)
- Each agent has a clear status and deliverable
- Design happens while building — visual output appears as agents work
- Package installation, file creation, and testing all visible

**Key insight:** Task-based visualization makes parallel agents comprehensible.

## Vercel v0.dev

**Pattern:** Prompt-to-component + iterative refinement.

- Natural language prompt → React + Tailwind code
- Live preview updates as you iterate
- Follow-up prompts refine specific elements
- Code and preview always in sync

**Key insight:** The output (live preview) is the interface. Code is secondary.

## Common Patterns Across All Products

1. **Output-first** — The generated content is the primary visual element
2. **Real-time streaming** — Content appears progressively, not all at once
3. **Inline editing** — Users can modify AI output directly
4. **Version history** — Easy to compare and revert
5. **Interruptibility** — Users can stop or redirect the AI mid-process
