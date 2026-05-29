# FeatureBlocks — Task Breakdown

## How to Use This File

Workflow per task:
1. **Write tests FIRST** (red phase) — no implementation yet
2. **Implement until tests pass** (green phase)
3. **Review the diff manually** — check for unintended changes
4. **Commit** with a descriptive message referencing the task
5. **Update CLAUDE.md / AGENTS.md** if you discovered something non-obvious (compound loop)

Evidence gates: each phase must have ✅ passing tests + 👀 human review before the next phase begins.

---

## Phase 0: Foundation ⬜

- [ ] Init Next.js 14 project with TypeScript and App Router (`npx create-next-app@latest`)
- [ ] Configure Tailwind CSS
- [ ] Install and configure ESLint (`eslint-config-next`) + Prettier
- [ ] Set up Vitest + React Testing Library (`vitest`, `@testing-library/react`, `@testing-library/user-event`)
- [ ] Write first smoke test: renders `<App />` without crashing
- [ ] Install dnd-kit (`@dnd-kit/core`, `@dnd-kit/sortable`, `@dnd-kit/utilities`)
- [ ] Install Zustand, Framer Motion, Radix UI primitives
- [ ] Set up Playwright for E2E (`npm init playwright@latest`)
- [ ] Configure GitHub Actions CI: install → lint → type-check → unit tests → build
- [ ] Review all AI config files (CLAUDE.md, AGENTS.md, copilot-instructions.md)

**Evidence gate**: CI passing on `main`, smoke test green ✅

---

## Phase 1: Block Registry & Data Model ⬜

- [ ] Define `Block` TypeScript type: `id`, `type`, `props`, `order` (see `docs/spec.md`)
- [ ] Define `BlockDefinition` type: `type`, `label`, `icon`, `defaultProps`, `schema`
- [ ] Create block registry in `src/lib/blocks/registry.ts` — map of `type → BlockDefinition`
- [ ] Write unit tests for registry: register block, lookup by type, list all blocks
- [ ] Implement `HeroBlock` definition (type, label, defaultProps: `headline`, `subheadline`, `ctaText`, `ctaHref`, `backgroundImage`)
- [ ] Implement `FeatureGridBlock` definition (type, label, defaultProps: `title`, `features[]` with `icon`, `title`, `description`)
- [ ] Implement `TestimonialsBlock` definition
- [ ] Implement `PricingBlock` definition
- [ ] Implement `CTABannerBlock` definition
- [ ] Write unit tests for each block definition: validates required props, returns correct defaults

**Evidence gate**: all registry unit tests green, 5 blocks registered ✅

---

## Phase 2: Canvas & Drag-and-Drop ⬜

- [ ] Create Zustand store `useCanvasStore`: `blocks[]`, `addBlock`, `removeBlock`, `reorderBlocks`, `updateBlockProps`
- [ ] Write unit tests for all store actions (add, remove, reorder, update)
- [ ] Build `<Canvas>` component: renders ordered list of blocks, accepts drop events
- [ ] Build `<CanvasBlock>` component: wraps a block, shows drag handle + delete button on hover
- [ ] Implement `useDragSensors` hook wrapping dnd-kit PointerSensor + KeyboardSensor
- [ ] Add `<SortableCanvasBlock>` using `@dnd-kit/sortable` — reorder via drag
- [ ] Write RTL tests for `<Canvas>`: renders empty state, renders multiple blocks in order, handles reorder
- [ ] Build `<BlockPalette>` sidebar: scrollable list of all registered blocks
- [ ] Each palette item is draggable (dnd-kit `useDraggable`) — dragging onto canvas calls `addBlock`
- [ ] Write RTL tests for `<BlockPalette>`: renders all registered blocks, displays label + icon
- [ ] E2E test (Playwright): drag a block from palette onto canvas → block appears on canvas

**Evidence gate**: E2E drag-and-drop test passing, all RTL tests green ✅

---

## Phase 3: Block Rendering & Inline Editing ⬜

- [ ] Build `<HeroBlock>` React component — renders headline, subheadline, CTA button, bg image
- [ ] Build `<FeatureGridBlock>` React component — responsive grid of feature cards
- [ ] Build `<TestimonialsBlock>` React component — carousel or grid of testimonial cards
- [ ] Build `<PricingBlock>` React component — tiered pricing table
- [ ] Build `<CTABannerBlock>` React component — full-width banner with headline + CTA
- [ ] Write snapshot tests for each block component with default props
- [ ] Build `<PropsEditor>` panel: appears in right sidebar when a block is selected
- [ ] `<PropsEditor>` renders text inputs / toggles / color pickers derived from block schema
- [ ] Editing a prop updates `useCanvasStore` → block re-renders immediately (live preview)
- [ ] Write RTL tests for `<PropsEditor>`: input change propagates to store

**Evidence gate**: all 5 blocks render correctly, live editing works in browser ✅

---

## Phase 4: Export ⬜

- [ ] Implement `exportToHTML(blocks: Block[]): string` in `src/lib/export/html.ts`
  - Renders each block to static HTML using `ReactDOMServer.renderToStaticMarkup`
  - Inlines Tailwind classes (or extracts CSS)
- [ ] Implement `exportToJSON(blocks: Block[]): string` — serialized canvas state
- [ ] Implement `importFromJSON(json: string): Block[]` — restore canvas from JSON
- [ ] Write unit tests for export/import round-trip (export then re-import → identical state)
- [ ] Add "Export HTML" and "Export JSON" buttons to the toolbar
- [ ] Add "Import JSON" button to restore a saved canvas
- [ ] E2E test: build a 2-block page → export HTML → validate HTML contains block content

**Evidence gate**: export round-trip test passing, exported HTML is valid ✅

---

## Phase 5: Polish & Harden ⬜

- [ ] Add Framer Motion enter/exit animations to canvas block add/remove
- [ ] Add undo/redo to `useCanvasStore` (using Zustand middleware or immer)
- [ ] Write unit tests for undo/redo (add → undo → assert empty, add → add → undo → assert one block)
- [ ] Keyboard accessibility: all interactive elements reachable by Tab, blocks reorderable by keyboard
- [ ] Run Playwright accessibility audit (`@axe-core/playwright`) and fix violations
- [ ] Mobile-responsive layout: sidebar collapses to bottom drawer on small screens
- [ ] Add loading skeleton for block palette
- [ ] Performance: memoize block components, profile with React DevTools

**Evidence gate**: axe audit passes, lighthouse score ≥ 90 ✅

---

## Phase 6: Ship ⬜

- [ ] Write `docs/spec.md` final review — check all requirements are met (checkboxes)
- [ ] Update README: add real screenshots / demo GIF
- [ ] Set up Vercel deployment (connect repo, configure `next build`)
- [ ] Configure preview deployments for PRs
- [ ] Tag `v0.1.0` release with changelog

**Evidence gate**: live demo URL available, all CI checks green ✅

---

## Parking Lot 🅿️

- Custom block creation UI (user-defined blocks)
- Block theme system (light/dark, brand colors)
- Collaborative editing (Yjs / Liveblocks)
- Block marketplace / sharing
- AI-assisted copy generation for blocks
- Publish directly to a hosted URL (Vercel/Netlify integration)

---

## Lessons Learned 📝

_Update this section as you discover non-obvious things about the codebase or toolchain._

- (empty — add as you go)
