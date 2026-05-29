# FeatureBlocks — Feature Specification

## Overview

FeatureBlocks is a browser-based drag-and-drop UI builder for composing landing page and marketing feature sections. Users select pre-built "blocks" (Hero, Feature Grid, Testimonials, Pricing, CTA Banner) from a palette, arrange them on a canvas by drag-and-drop, edit their content inline via a props panel, and export the result as static HTML or JSON.

**Problem statement**: Building a marketing landing page requires either hand-coding repetitive React/HTML sections or paying for heavyweight no-code tools. FeatureBlocks gives developers and marketers a lightweight, open, composable builder that exports clean, copy-pasteable HTML they actually own.

---

## Functional Requirements

### Block Palette
- [ ] Displays all registered block types with a label and icon
- [ ] Each block is draggable onto the canvas
- [ ] Palette is scrollable and searchable (Phase 5)
- [ ] Palette shows a thumbnail preview on hover

### Canvas
- [ ] Renders the current list of blocks in order
- [ ] Accepts block drops from the palette (adds block to bottom, or at drop position)
- [ ] Supports drag-to-reorder existing blocks
- [ ] Shows empty state with instructions when no blocks are present
- [ ] Each block shows a drag handle, edit indicator, and delete button on hover
- [ ] Clicking a block opens its props in the right sidebar editor

### Block Rendering
- [ ] **HeroBlock**: headline, subheadline, CTA button (text + href), optional background image URL, text alignment (left/center)
- [ ] **FeatureGridBlock**: section title, 2–6 feature cards (each: icon name, title, description), columns (2 or 3)
- [ ] **TestimonialsBlock**: section title, 1–6 testimonials (each: quote, author name, role, avatar URL)
- [ ] **PricingBlock**: section title, 2–3 tiers (each: name, price, period, features[], CTA text, highlighted flag)
- [ ] **CTABannerBlock**: headline, body text, primary CTA (text + href), optional secondary CTA, background color

### Props Editor
- [ ] Renders a form derived from the selected block's schema
- [ ] Supports field types: text, textarea, url, color, boolean toggle, number, array-of-objects (repeatable rows)
- [ ] All edits are immediately reflected in the canvas (live preview, no "Apply" button)
- [ ] Shows field labels and optional helper text
- [ ] Validates required fields and shows inline errors

### Export
- [ ] **Export HTML**: renders all blocks to static HTML with inlined Tailwind classes; downloads as `page.html`
- [ ] **Export JSON**: serializes canvas state; downloads as `feature-blocks.json`
- [ ] **Import JSON**: file picker; restores canvas from a previously exported JSON file
- [ ] Exported HTML is standalone — no runtime JS required

### Undo / Redo
- [ ] Cmd/Ctrl+Z undoes the last canvas action (add, remove, reorder, prop change)
- [ ] Cmd/Ctrl+Shift+Z (or Cmd+Y) redoes
- [ ] History depth: 50 steps

---

## Non-Functional Requirements

- [ ] First Contentful Paint < 1.5s on fast 3G
- [ ] Canvas handles 20+ blocks without noticeable jank (60 fps drag)
- [ ] Fully keyboard-accessible (WCAG 2.1 AA)
- [ ] Works in Chrome 110+, Firefox 110+, Safari 16+, Edge 110+
- [ ] No external analytics or tracking
- [ ] All state is local (no backend required in v1)

---

## Data Model

```typescript
// A placed block instance on the canvas
interface Block {
  id: string;           // nanoid — unique per instance
  type: BlockType;      // e.g. "hero", "feature-grid"
  props: BlockProps;    // freeform config object (type-safe per block)
  order: number;        // integer, 0-indexed, controls render order
}

// A block type definition (static, registered at startup)
interface BlockDefinition<P extends BlockProps = BlockProps> {
  type: BlockType;
  label: string;
  icon: string;           // Lucide icon name
  description: string;
  defaultProps: P;
  schema: BlockSchema;    // drives the PropsEditor form
}

// Schema field descriptor (drives PropsEditor form generation)
interface SchemaField {
  key: string;
  label: string;
  type: 'text' | 'textarea' | 'url' | 'color' | 'boolean' | 'number' | 'array';
  required?: boolean;
  helperText?: string;
  itemSchema?: SchemaField[];   // for type: 'array'
}

// Canvas store shape
interface CanvasStore {
  blocks: Block[];
  selectedBlockId: string | null;
  history: Block[][];     // for undo
  future: Block[][];      // for redo
  addBlock: (type: BlockType, atIndex?: number) => void;
  removeBlock: (id: string) => void;
  reorderBlocks: (activeId: string, overId: string) => void;
  updateBlockProps: (id: string, patch: Partial<BlockProps>) => void;
  selectBlock: (id: string | null) => void;
  undo: () => void;
  redo: () => void;
}
```

---

## API / Interface Design

### Block Registry

```typescript
// src/lib/blocks/registry.ts
const registry = new BlockRegistry();
registry.register(heroBlockDefinition);
registry.register(featureGridBlockDefinition);

registry.getAll(): BlockDefinition[]
registry.get(type: BlockType): BlockDefinition | undefined
```

### Export Utilities

```typescript
// src/lib/export/html.ts
exportToHTML(blocks: Block[]): string

// src/lib/export/json.ts
exportToJSON(blocks: Block[]): string
importFromJSON(json: string): Block[]   // throws on invalid JSON
```

---

## Test Plan

### Unit Tests (Vitest)
- Block registry: register, lookup, list all, duplicate type throws
- Each block definition: default props match schema, all required keys present
- Zustand store actions: add, remove, reorder, update, undo, redo, history depth cap
- Export/import round-trip: `importFromJSON(exportToJSON(blocks))` deep-equals `blocks`
- `exportToHTML`: output contains expected text content from block props

### Component Tests (React Testing Library)
- `<BlockPalette>`: renders all 5 block types with labels
- `<Canvas>`: renders empty state, renders N blocks in correct order
- `<CanvasBlock>`: shows delete button on hover, fires `removeBlock` on click
- `<PropsEditor>`: text input change calls `updateBlockProps` with correct patch
- Each block component: renders with default props, renders with custom props, matches snapshot

### E2E Tests (Playwright)
- Drag block from palette → verify it appears on canvas
- Reorder two blocks by drag → verify new order persists
- Edit headline in PropsEditor → verify canvas updates live
- Export JSON → Import JSON → verify canvas state is identical
- Keyboard navigation: Tab through all palette items, use Enter to add, use arrow keys to reorder

---

## Open Questions

1. Should exported HTML include a `<style>` block with extracted Tailwind classes, or require the user to include the Tailwind CDN? (Decision needed before Phase 4)
2. Should block instances support multiple themes (light/dark variants) per block, or is a single design per block sufficient for v1?
3. Is local storage auto-save in scope for v1, or is manual JSON export sufficient?
4. Should the props editor support rich text (bold/italic) for headline fields, or plain text only in v1?
