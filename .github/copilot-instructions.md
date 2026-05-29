# GitHub Copilot Instructions — FeatureBlocks

## Stack
- **Framework**: Next.js 14+ with App Router
- **Language**: TypeScript (strict mode)
- **Styling**: Tailwind CSS only — no CSS modules, no styled-components
- **State**: Zustand (single store: `useCanvasStore`)
- **Drag & Drop**: dnd-kit (`@dnd-kit/core`, `@dnd-kit/sortable`)
- **Animations**: Framer Motion
- **UI Primitives**: Radix UI
- **Testing**: Vitest + React Testing Library + Playwright

## Coding Conventions

### Components
- Always add `'use client'` to interactive components (they all use hooks/context)
- Props interface: `interface <Name>Props { ... }` above the component
- Use `cn()` (clsx + tailwind-merge) for conditional class names
- Add `data-block-type="<type>"` to the root element of every block component
- Add `data-block-id={block.id}` to every `<CanvasBlock>` wrapper

### State
- All canvas state mutations go through `useCanvasStore` actions — never mutate state directly
- Block components are purely presentational — they receive `props` and render; they do NOT call `useCanvasStore`
- `DndContext` must wrap the entire builder layout, not just the canvas

### Types
- Import all types from `src/types/index.ts`
- Never use `any` — use `unknown` and type-narrow if needed
- Explicit return types on all exported functions and hooks

### Exports (HTML)
- Export functions live in `src/lib/export/` and are pure — no React hooks, no side effects
- Use `ReactDOMServer.renderToStaticMarkup` to render blocks to HTML

## Testing Conventions
- Write the test file BEFORE implementing the feature (TDD)
- Reset Zustand store in `beforeEach`: `useCanvasStore.setState(initialState)`
- Prefer accessible selectors (`getByRole`, `getByLabelText`) over `getByTestId`
- Playwright selectors: use `[data-block-type]` and `[data-block-id]` attributes

## Boundaries
- Do NOT refactor existing working code unless explicitly asked
- Do NOT remove or skip existing tests
- Do NOT add a new dependency without mentioning it in your response
- Do NOT use `any` type — if you're unsure of a type, use `unknown` and ask
- Do NOT add backend code — this is a fully client-side app in v1
- Do NOT build features not listed in `docs/spec.md` without confirmation
- Always check `TODO.md` for the current phase before starting work
