# Generative UI: How Leading Products Do It

## Claude Artifacts (Anthropic)

**Pattern:** The artifact IS the workspace. Not a panel. Not a sidebar.

- Artifacts appear inline but expand to a full workspace
- Runtime sandbox — code executes live
- Users edit artifacts directly (control transfer loop)
- Version history with diff view

**Key insight:** The artifact replaces chat as the primary interface. Chat becomes secondary.

## ChatGPT Canvas (OpenAI)

**Pattern:** Persistent side panel + inline editing + version history.

- Canvas opens as side panel for focused editing
- Highlight any text → AI suggestions inline
- Version history with one-click restore
- One-click shortcuts ("Make it longer", "Change tone")

**Key insight:** Best of both worlds — dedicated editing space without leaving the conversation.

## Cursor Agent UI

**Pattern:** Agent thinking visible + code streams file-by-file.

- Agent's reasoning visible as it plans
- Code changes stream in real-time
- User can interrupt with follow-up at any point
- Terminal output streams live

**Key insight:** Transparency builds trust. Watching the agent think makes users comfortable.

## Replit Agent 4

**Pattern:** Trello-style progress board.

- Tasks in columns: Drafts → Active → Ready → Done
- Each card: agent name, task, progress
- Design previews appear as agents work

**Key insight:** Task-based visualization makes parallel agents comprehensible.

## Vercel v0.dev

**Pattern:** Prompt-to-component + iterative refinement.

- Natural language → React + Tailwind code
- Live preview updates as you iterate
- Follow-up prompts refine specific elements

**Key insight:** The output (live preview) IS the interface. Code is secondary.

## Common Patterns

1. **Output-first** — Generated content is the primary visual element
2. **Real-time streaming** — Content appears progressively
3. **Inline editing** — Users modify AI output directly
4. **Version history** — Easy compare and revert
5. **Interruptibility** — Stop/redirect at any moment
