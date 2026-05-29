# CLAUDE.md — FeatureBlocks

Context for AI agents. Keep this file under 200 lines. Update it when you discover non-obvious conventions.

---

## Commands

```bash
npm run dev          # Next.js dev server on :3000
npm run build        # Production build
npm run start        # Serve production build
npm run test         # Vitest unit + component tests (watch mode)
npm run test:run     # Vitest single-run (for CI)
npm run test:e2e     # Playwright E2E tests
npm run lint         # ESLint
npm run type-check   # tsc --noEmit
npm run format       # Prettier write
```

> **TODO**: fill in actual scripts once `package.json` is initialized in Phase 0.

---

## Directory Map

```
src/
  app/                Next.js App Router — pages, layouts, metadata
  components/
    blocks/           One file per block type (HeroBlock.tsx, etc.) — ONLY rendering, no drag logic
    canvas/           Canvas.tsx, CanvasBlock.tsx, SortableCanvasBlock.tsx
    sidebar/          BlockPalette.tsx, PropsEditor.tsx
    ui/               Shared primitives: Button, Input, Toggle, ColorPicker — no business logic
  lib/
    blocks/           registry.ts (BlockRegistry class), one file per block definition
    dnd/              sensors.ts (useDragSensors hook), helpers
    export/           html.ts, json.ts — pure functions, no React
  hooks/              Custom hooks: useUndoRedo.ts, useBlockSelection.ts
  store/              useCanvasStore.ts — single Zustand store
  types/              index.ts — all shared TypeScript types exported from one file
tests/                Mirrors src/ structure exactly
docs/
  spec.md             Source of truth for requirements — check before implementing
  adr/                Architecture decisions
```

---

## Key Conventions

### State
- **Single source of truth**: `useCanvasStore` (Zustand) owns all canvas state.
- Block components are **pure/dumb** — they receive props and render. They do NOT read from the store directly.
- The canvas layer (`CanvasBlock`, `SortableCanvasBlock`) connects store → block component.

### TypeScript
- All shared types live in `src/types/index.ts`. Import from there, not from component files.
- `BlockProps` per block type is defined in its definition file in `src/lib/blocks/`, not in the component.
- No `any`. Use `unknown` and narrow with type guards if needed.

### Drag-and-Drop (dnd-kit)
- Use `DndContext` wrapping the entire builder layout (not just canvas).
- Palette items use `useDraggable`. Canvas items use `useSortable`.
- `onDragEnd` lives in the top-level layout — dispatches to the store.
- Active drag state is tracked in `DndContext`, not in Zustand.

### Styling
- Tailwind only. No CSS modules, no styled-components.
- Block components must include a `data-block-type` attribute for Playwright selectors.
- Canvas items get a `data-block-id` attribute.

### Testing
- Component tests import from `@testing-library/react` + `@testing-library/user-event`.
- Zustand stores must be **reset between tests** — call `useCanvasStore.setState(initialState)` in `beforeEach`.
- Use `data-testid` sparingly — prefer accessible role/label selectors.
- Snapshot tests live alongside unit tests, not in a separate folder.

---

## Workflow (Read This First)

1. **Run the tests**: `npm run test:run` — understand what's passing before touching anything.
2. **Check TODO.md**: find the next unchecked item in the current phase.
3. **Read docs/spec.md**: the spec is the source of truth for what "done" means.
4. **Write failing tests first** (red), then implement (green).
5. **Type-check before committing**: `npm run type-check`.
6. **Commit per task**, not per file.
7. **Update this file** if you learn something non-obvious.

---

## Gotchas

- dnd-kit requires `<DndContext>` to be above both the drag source and drop target in the tree — don't put it inside the canvas only.
- `ReactDOMServer.renderToStaticMarkup` is used for HTML export — it strips event handlers, which is intentional.
- Next.js App Router: server components can't use hooks or context. All interactive builder components must be in `'use client'` files.
- Zustand stores with undo/redo history can get large — cap history depth at 50 in the store, not in the actions.
- Tailwind's JIT purges classes not found in source — if you dynamically construct class names (e.g. `bg-${color}-500`), use `safelist` in `tailwind.config.ts`.
