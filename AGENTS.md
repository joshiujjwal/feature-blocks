# AGENTS.md — FeatureBlocks

Instructions for OpenAI Codex and other AI coding agents.

---

## Setup

```bash
# Install dependencies
npm install

# Run dev server
npm run dev

# Verify everything works
npm run test:run && npm run type-check && npm run lint
```

> **TODO**: Update commands once package.json is initialized (Phase 0).

---

## Code Style

### TypeScript
- Strict mode enabled (`"strict": true` in tsconfig)
- No `any` — use `unknown` and narrow with guards
- All shared types in `src/types/index.ts`
- Use `interface` for object shapes, `type` for unions/aliases
- Explicit return types on exported functions

### React / Next.js
- Functional components only — no class components
- All interactive components must have `'use client'` directive (App Router)
- Props interfaces named `<ComponentName>Props`
- Extract complex logic into custom hooks in `src/hooks/`
- Keep components focused: if a component has >150 lines, split it

### Naming
- Components: `PascalCase.tsx`
- Hooks: `useCamelCase.ts`
- Utilities/lib: `camelCase.ts`
- Types: `PascalCase` (interfaces and types)
- Test files: `<filename>.test.tsx` co-located in `tests/` mirror

### Tailwind
- No inline `style` attributes — use Tailwind classes
- No dynamic class construction unless using `cn()` (clsx + tailwind-merge)
- Responsive prefixes: `sm:`, `md:`, `lg:` — mobile-first

---

## Testing

### Rules
1. **Write tests FIRST** — failing test before any implementation
2. **Run existing tests** before starting any task: `npm run test:run`
3. Never delete a test — if a test is wrong, fix the test (or the code)
4. Every exported function must have at least one unit test
5. Every interactive component must have at least one RTL test
6. New E2E tests go in `tests/e2e/`

### Running Tests
```bash
npm run test         # Watch mode (development)
npm run test:run     # Single run (CI / before commit)
npm run test:e2e     # Playwright E2E
npm run test:e2e -- --ui   # Playwright with UI
```

### Test Conventions
- Reset Zustand store state in `beforeEach`
- Use `userEvent` over `fireEvent` for user interactions in RTL tests
- Playwright: use `data-block-type` and `data-block-id` attributes as selectors, not text content
- Snapshot tests: run `npm run test -- --update-snapshots` to update, review diff in PR

---

## PR Instructions

Every PR must include evidence:
- **Unit/component tests**: paste `npm run test:run` output showing all tests passing
- **Type check**: paste `npm run type-check` output (no errors)
- **Lint**: paste `npm run lint` output (no errors)
- **For UI changes**: include a screenshot or screen recording
- **For drag-and-drop changes**: include a Playwright E2E test result
- **For export changes**: include a sample of exported HTML/JSON output

Keep PRs atomic — one logical change per PR. If you find a bug while working, open a separate PR to fix it rather than bundling it in.

---

## Architecture Constraints

- Do **not** introduce a backend or database — v1 is fully client-side
- Do **not** add a new state management library — use Zustand only
- Do **not** replace dnd-kit with another drag library without an ADR in `docs/adr/`
- Do **not** add CSS-in-JS (styled-components, emotion) — Tailwind only
- Do **not** remove accessibility attributes (`aria-*`, `role`, `data-*`) without explicit instruction
