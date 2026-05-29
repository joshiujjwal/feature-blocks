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
