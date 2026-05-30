# FeatureBlocks

> 🚧 **Status: Early Development**

A drag-and-drop UI builder for composing landing page and marketing feature sections visually. Pick from a library of pre-built "feature blocks" (hero, pricing, testimonials, CTA, etc.), drag them onto a canvas, configure their content inline, then export clean React/HTML.

---

## Tech Stack

| Layer | Choice |
|---|---|
| Framework | Next.js 14+ (App Router) |
| Language | TypeScript |
| UI | React 18 + Tailwind CSS |
| Drag & Drop | dnd-kit |
| State | Zustand |
| Animations | Framer Motion |
| Primitives | Radix UI |
| Tests | Vitest + React Testing Library |
| E2E | Playwright |
| Linting | ESLint + Prettier |

---

## Getting Started

```bash
# Clone
git clone git@github.com:joshiujjwal/feature-blocks.git
cd feature-blocks

# Install
npm install

# Dev server
npm run dev

# Tests
npm run test

# Type check
npm run type-check

# Lint
npm run lint
```

Open [http://localhost:3000](http://localhost:3000) in your browser.

---

## Project Structure

```
feature-blocks/
├── src/
│   ├── app/                  # Next.js App Router pages & layouts
│   ├── components/
│   │   ├── blocks/           # Individual block components (Hero, Pricing, etc.)
│   │   ├── canvas/           # Drop canvas — the visual composition area
│   │   ├── sidebar/          # Block palette sidebar (draggable block list)
│   │   └── ui/               # Shared UI primitives (Button, Input, etc.)
│   ├── lib/
│   │   ├── blocks/           # Block registry & type definitions
│   │   ├── dnd/              # Drag-and-drop helpers & sensors
│   │   └── export/           # Export page to React/HTML utilities
│   ├── hooks/                # Custom React hooks
│   ├── store/                # Zustand stores (canvas state, block config)
│   └── types/                # Shared TypeScript types
├── tests/                    # Test suite (mirrors src/)
├── docs/
│   ├── spec.md               # Feature specification
│   └── adr/                  # Architecture Decision Records
├── .github/
│   └── copilot-instructions.md
├── CLAUDE.md                 # Context for Claude / AI agents
├── AGENTS.md                 # Setup & conventions for OpenAI Codex agents
└── TODO.md                   # Evidence-gated task breakdown
```

---

## Contributing

- **Red/Green TDD**: write failing tests first, then implement
- **Evidence in PRs**: include test output, screenshots, or recordings
- **Small PRs**: one logical change per PR
- **No unreviewed code ships**: every PR needs at least one human review
- **Update context files**: if you learn something non-obvious, add it to `CLAUDE.md`

---

## 🚀 Improvement Proposals

### First-Principles Analysis
- **The hard problem is block composability, not drag-and-drop**: dnd-kit handles the interaction model well; the real engineering challenge is defining a block schema that is expressive enough to cover real use cases while remaining simple enough for clean export output.
- **Export quality determines real-world usefulness**: A builder that exports messy, hard-to-maintain JSX/HTML is a toy; the export pipeline needs to produce code that a developer would actually commit to their project without cleanup.
- **State complexity grows non-linearly with block count**: A Zustand store managing 20 nested, configurable blocks with undo/redo and inline editing becomes extremely complex; the state shape needs deliberate design before implementation, not after.
- **Competing with no-code incumbents**: Framer, Webflow, and even Tailwind UI cover this space well; the differentiation must be narrow and specific (e.g., "developer-first, outputs clean React components") — breadth is the wrong strategy early.

### Key Risks & Assumptions
- **Inline content editing is a solved-but-hard problem**: Contenteditable elements in React have well-documented quirks (cursor jumps, IME issues, controlled vs. uncontrolled conflicts); using a library like Tiptap or Plate is safer than rolling custom inline editing.
- **Export format lock-in**: Exporting to React assumes the user's codebase uses React + Tailwind; an HTML-only export path is needed for non-React projects, and the schema must support both without duplication.
- **Accessibility of the builder itself**: A drag-and-drop canvas built purely on mouse interaction excludes keyboard-only users; Radix UI helps with primitives but the canvas DnD still needs ARIA roles and keyboard shortcuts.
- **No persistence layer**: The README lists no database or storage — a user who refreshes the page loses their work; even localStorage autosave is a required baseline for any real adoption.

### Concrete Improvement Ideas
- **Define and stabilise the block JSON schema first**: Before building the canvas or sidebar, write out the exact JSON structure for each block type (Hero, Pricing, CTA) and validate it produces acceptable exported code — this is the contract everything else depends on.
- **Add autosave to localStorage with undo/redo (Ctrl+Z)**: Use Zustand middleware (`zustand/middleware` with `temporal` from `zundo`) to get undo/redo for free; persist canvas state to localStorage on every change — these two features prevent the most common frustrations.
- **Build the export pipeline before the full block library**: Implement clean React + Tailwind export for just 2 blocks (Hero + CTA) end-to-end before adding more blocks — validates the schema and export quality early, before accruing content debt.
- **Add a "preview in new tab" mode**: Render the current canvas state as a standalone HTML page without the builder chrome — lets users see exactly what their exported page will look like; directly reduces the feedback loop from design to ship.
- **Implement a block variant system**: Each block type (e.g., Hero) should support 2–3 style variants (centered, split, dark/light) selectable from the sidebar — multiplies the design surface without multiplying implementation cost.
- **Publish a curated block registry as a JSON file**: List all available blocks with name, screenshot thumbnail, and required props in a single JSON file — enables future features (search, categories, community blocks) and documents the block API without prose.
