# 🚫 Anti-Slop Discipline & Aesthetic Constraints

## Level 1 Non-Negotiable Invariants (MUST)

- ❌ **No 3px Left-Border Alert Stripes**: Never put a 3px colored left border on alerts, cards, or dialogs (dated Bootstrap/AI trope). Replace with clean structured trays with subtle hairline borders.
- ❌ **No Cartoon Warning Badges**: Never place cartoon warning triangles in bright square badges on destructive confirmation modals. Use clean editorial headers with subtle status pill tags (`[CRITICAL]`).
- ❌ **No Neon Glowing Blobs**: Never add random purple/blue background blobs or artificial gradients behind dark mode cards.
- ❌ **No Inconsistent Border Soup**: Avoid wrapping every UI element in a generic 1px gray border. Prefer whitespace, background contrast, or subtle shadows first.
- ❌ **No Arbitrary Radii Mixing**: Never mix sharp 0px corners, 8px rounded buttons, and 24px pills haphazardly in the same container.

---

## Level 2 Design Preferences (SHOULD)

- **Grayscale First**: If an interface does not look balanced, legible, and structured in pure black & white, color will only mask fundamental hierarchy flaws.
- **Personality Consistency**:
  - `0px`: Serious, technical, high-density financial/enterprise.
  - `6px`–`8px`: Modern neutral (default safe workhorse).
  - `12px`–`16px` / `9999px` (Pill): Approachable, playful, consumer-facing.
- **Nested Corner Radius Formula**:
  - Inner corners inside a padded container must have a smaller radius to prevent awkward gap distortions:
  $$\text{Radius}_{\text{inner}} = \text{Radius}_{\text{outer}} - \text{Padding}$$
  - *Example: Outer card radius = 12px, Padding = 4px $\to$ Inner image/button radius = 8px.*
