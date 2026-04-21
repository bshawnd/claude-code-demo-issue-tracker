# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Environment

Running on Windows with WSL (Ubuntu). Always use `zsh` for shell commands — run them inside WSL, not PowerShell or CMD.

## Commands

```bash
bun run dev       # Start dev server at localhost:3000
bun run build     # Production build
bun run typecheck # Type check (tsc --noEmit)
bun install       # Install dependencies
```

No lint or test scripts are configured.

## Architecture

Next.js 15 App Router kanban board. Four status columns: Backlog, Todo, In Progress, Done.

**Key directories:**
- `app/api/` — REST API routes (issues CRUD + column reorder)
- `components/` — Client components: Board (state/DnD orchestration), Column (droppable), IssueCard (draggable)
- `lib/` — `types.ts` (Issue, Status types) and `store.ts` (in-memory singleton)

**Data flow:**
- `Board.tsx` owns all client state; fetches from `/api/issues` on mount
- Drag-and-drop via dnd-kit; on drop calls `PUT /api/columns/:status/reorder`
- Backend uses an in-memory `IssueStore` singleton — data resets on server restart
- `globalThis` workaround in `store.ts` preserves the store across Next.js dev hot-reloads

**API routes:**
- `GET/POST /api/issues` — list all / create
- `GET/PATCH/DELETE /api/issues/[id]` — single issue operations
- `PUT /api/columns/[status]/reorder` — reorder issues within a column

**No database, auth, or persistence** — this is an intentionally minimal demo.
