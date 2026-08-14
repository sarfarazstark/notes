---
name: ultimate-ui
description: >-
  Use this skill whenever a task involves designing, building, styling, reviewing, critiquing,
  or refactoring user interfaces, web apps, components, mobile screens, cards, buttons, forms,
  tables, modals, navigation, typography, spacing, color palettes, dark mode, or design systems.
  Trigger on requests like "design a UI", "build a component", "make this look better", "why does this UI feel off",
  "review my styling", "build a dashboard", or any task touching HTML/CSS/Tailwind styling.
---

# 🛠️ Ultimate UI: Execution Controller

## 🚨 Execution Policy
This is an **active implementation skill**. Apply every rule that is relevant to the current task. Do not require the user to explicitly request an applicable rule, but do not force rules whose prerequisites are absent.

---

## 🎯 Rule Priority Hierarchy

### Level 1 — Non-Negotiable Invariants (MUST)
- **Anti-Slop**: Never use 3px colored alert stripes, cartoon warning badges, or decorative neon blobs. (See [anti-slop.md](references/anti-slop.md))
- **Accessible Focus**: Always provide high-contrast `:focus-visible` rings (`box-shadow: 0 0 0 2px var(--bg), 0 0 0 4px var(--text)`).
- **Target Ergonomics**: Minimum interactive target size is `32px` on desktop pointer and `44px` on touch screens. (See [components.md](references/components.md))
- **Consistent Radii**: Never mix mismatched border radii arbitrarily.
- **Consistent Spacing**: Use the 25% spacing scale (`4, 8, 12, 16, 24, 32, 48, 64, 96, 128px`). (See [spacing.md](references/spacing.md))

### Level 2 — Core Design Preferences (SHOULD)
- **Library Selection**: Prefer unstyled headless primitives (Radix UI, React Aria) or owned code (shadcn/ui, HeroUI Native) over locked-in black-box component libraries. (See [library-selection.md](references/library-selection.md))
- **Titanium Dark Mode**: Prefer titanium off-white (`#ececf1`) for headings and zinc (`#d4d4d8`) for body text over stark `#ffffff`. (See [typography.md](references/typography.md))
- **Optical Tracking**: Tighten headings ($>20\text{px}$) tracking (`-0.02em` to `-0.035em`); widen small uppercase labels ($\le 12\text{px}$) (`+0.05em` to `+0.1em`).
- **Action Pyramid**: Prefer one dominant primary CTA per screen; de-emphasize secondary actions.
- **Dual-Theme Tokens**: Separate primitive, semantic, and component tokens. (See [colors.md](references/colors.md))
- **Grayscale First**: Ensure hierarchy and contrast work in black & white before relying on color.

### Level 3 — Contextual Rules (APPLY WHEN APPLICABLE)
- **IF UI has raised cards/buttons** $\to$ Apply the 4-layer lighting model (top bevel highlight, face micro-gradient, soft bottom occlusion, dual-layer shadow). (See [depth.md](references/depth.md))
- **IF UI has numerical metrics, timers, or tables** $\to$ Apply `font-variant-numeric: tabular-nums;`.
- **IF UI has nested rounded containers** $\to$ Apply the nested radius formula: $R_{\text{inner}} = R_{\text{outer}} - \text{padding}$.
- **IF UI has destructive dialogs** $\to$ Use high elevation, an editorial header, and a structured impact tray (`.dialog-impact-card`). (See [components.md](references/components.md))
- **IF UI has sticky/floating surfaces** $\to$ Apply frosted vibrancy (`backdrop-filter: blur(16px)`).
- **IF text truncation occurs inside flex layouts** $\to$ Apply `min-width: 0;` to the flex child.
- **IF dynamic loading states occur** $\to$ Reserve skeleton dimensions to ensure zero layout shift.

---

## 📋 Pre-Flight Audit Checklist

Only audit rules that are applicable to the current implementation.

### Always Check:
- [ ] No anti-slop patterns are present (no 3px stripes, no cartoon warning icons, no neon blobs).
- [ ] Interactive elements have accessible `:focus-visible` states and meet target sizes ($\ge 32\text{px}$ pointer, $\ge 44\text{px}$ touch).
- [ ] Visual hierarchy is intentional (contrast and weights balance appropriately).
- [ ] Spacing is consistent and proximity grouping is clear.

### Conditional Checks:
- [ ] **IF numerical metrics exist**: Metrics use `tabular-nums`.
- [ ] **IF dark mode is used**: Headings use titanium (`#ececf1`) to prevent glare.
- [ ] **IF raised cards/buttons are used**: Physical lighting and soft bottom occlusion are applied.
- [ ] **IF nested containers exist**: Inner radius satisfies $R_{\text{inner}} = R_{\text{outer}} - \text{padding}$.
- [ ] **IF destructive dialog exists**: Confirmation layout uses structured impact parameters without cartoon icons.
- [ ] **IF flex truncation exists**: Flex child has `min-width: 0`.
- [ ] **IF loading state exists**: Skeleton dimensions prevent layout shift.
