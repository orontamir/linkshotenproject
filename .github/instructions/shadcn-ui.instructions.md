---
description: "Use when building UI, adding components, writing JSX/TSX markup, buttons, forms, dialogs, inputs, cards, menus, or any visual element. Enforces shadcn/ui as the ONLY component source — no custom or hand-rolled components."
name: "shadcn/ui Component Rules"
applyTo: ["app/**", "components/**"]
---

# shadcn/ui Component Rules

All UI in this app is built with **shadcn/ui**. Do not hand-roll custom
components — always use a shadcn/ui component.

## Hard Rules

- **Never** create bespoke UI components (custom buttons, inputs, modals,
  dropdowns, cards, etc.) from raw HTML + Tailwind.
- If a shadcn/ui component exists for the need, use it.
- If the component isn't in the project yet, **add it** via the shadcn CLI
  before using it — don't reimplement it.
- Compose shadcn primitives to build features; wrapping shadcn components for
  app-specific composition is fine, replacing them is not.

## Where Components Live

- Installed components: `components/ui/` (import via `@/components/ui/*`).
- Utilities: `@/lib/utils` (use `cn()` for class merging).
- Icons: **lucide-react** only (`iconLibrary: lucide`).
- Config of record: [components.json](../../components.json) (style `base-nova`,
  RSC, CSS variables, neutral base color).

## Adding a Component

```bash
npx shadcn@latest add <component>
```

Then import from the `ui` alias:

```tsx
import { Button } from "@/components/ui/button";

<Button variant="outline">Save</Button>
```

## Styling

- Use component `variant`/`size` props and Tailwind utility classes.
- Merge conditional classes with `cn()` from `@/lib/utils` — do not concatenate
  class strings manually.
- Do not fork or edit generated `components/ui/*` files to create a "custom"
  variant when a prop or composition would do.
