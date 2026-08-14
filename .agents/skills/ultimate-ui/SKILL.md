---
name: ultimate-ui
description: >-
  Tactical UI/UX design heuristics, visual rules, and subtle 3D physical lighting systems distilled from Refactoring UI by Adam Wathan & Steve Schoger.
  Use whenever designing, building, reviewing, critiquing, or styling interfaces, web apps, components,
  dashboards, cards, buttons, forms, tables, color systems, typography scales, elevation/shadows, and spacing systems,
  even if the user never says "design principles." Trigger on requests like "design a dashboard," "make this look better,"
  "why does this UI feel off," "build a settings page," "review my component's styling," or any task involving layout,
  color palettes, type scales, shadows, or empty states. Pairs well with the frontend-design skill.
---

# 🛠️ Ultimate UI: Design Engineering Heuristics & Subtle 3D System

A condensed, production-grade decision framework and physical lighting model for building polished, high-craft, and tactile user interfaces.

---

## ⚡ How to Apply This During a Task (Decision Framework)

1. **Identify Relevant Sections**: Skim the core themes (e.g., Form $\to$ Hierarchy + Spacing + Typography; Cards/Buttons $\to$ 3D Lighting & Motion; Colors $\to$ Color Systems).
2. **Apply Concrete Tie-Breakers**: Use the specific rules below as instant decision-makers (e.g., *"Bigger font vs. bolder weight?"* $\to$ Bolder weight; *"Equal gap vs. parent-child gap?"* $\to$ Tight child gap, loose parent gap).
3. **Declare Trade-Offs**: When principles conflict (e.g., generous white space vs. high-density dashboard monitoring), explicitly state which constraint is being prioritized and why.

---

## 1. Starting Strategy, Personality & Anti-Slop Discipline

- **Design a real feature first, not the app shell**: Build the raw input fields, buttons, and actionable data for a specific user task (e.g. "search for a flight"). Navigation and container decisions only make sense once real content exists.
- **Hold the color (Low-Fidelity Grayscale First)**: Get layout, contrast, and spacing right in pure black & white first. If an interface doesn't work in grayscale, color will only mask structural flaws.
- **Commit to a clear visual voice (Anti-Slop Guidelines)**:
  - **No Generic Tropes**: Ban neon purple/blue glowing blobs on dark cards, generic stock placeholders, and uncurated border soup.
  - **No 3px Left-Border Stripes**: Never use 3px colored left border stripes on alerts/dialogs (dated Bootstrap/AI trope). Replace with clean structured impact trays with subtle hairline borders.
  - **No Cartoon Warning Badges**: Do not place generic cartoon warning triangles in tinted square badges on confirmation modals. Use clean editorial headers with subtle status pill tags (`[CRITICAL]`).
- **The Nested Corner Radius Formula**:
  - Inner corners inside a padded container must have a smaller radius to prevent awkward gap distortions:
  $$\text{Radius}_{\text{inner}} = \text{Radius}_{\text{outer}} - \text{Padding}$$
  - *Example: Outer card radius = 12px, Padding = 4px $\to$ Inner image/button radius = 8px.*
- **Personality Radii Scale**:
  - `0px`: Serious, technical, high-density financial/enterprise.
  - `6px`–`8px`: Modern neutral (default safe workhorse).
  - `12px`–`16px` / `9999px` (Pill): Approachable, playful, consumer-facing.
  - *Never mix mismatched radii haphazardly.*
- **Predefine constrained systems**: A small set of "obviously different" options beats an infinite range of arbitrary choices:
  - Spacing scale: `4, 8, 12, 16, 24, 32, 48, 64, 96, 128px`.
  - Type scale: `12, 14, 16, 18, 20, 24, 30, 36, 48px`.
  - Color shades: 9 distinct steps (`100` to `900`).
  - Shadows: 5 stepped elevations.

---

## 2. Visual Hierarchy, Contrast & Anti-Glare Typography

- **Hierarchy makes a UI feel designed**: Deliberately de-emphasize secondary/tertiary content instead of trying to make everything stand out.
- **Titanium Typography in Dark Mode (Anti-Glare Rule)**:
  - **Never use pure stark `#ffffff` for dark mode headings or body text** — it creates harsh eye glare.
  - **Display Headings**: Titanium off-white (`#ececf1`).
  - **Primary Body**: Soft slate / zinc (`#d4d4d8`).
  - **Secondary Labels**: Medium zinc (`#9ca3af` / `#a1a1aa`).
  - **Tertiary Metadata**: Muted gray (`#6b7280` / `#71717a`).
- **Avoid font weights $< 400$** for UI text — de-emphasize using lighter color or smaller font size instead. Two font weights (`400/500` normal, `600/700` bold) are usually plenty.
- **Grey on colored backgrounds**: Grey-on-white de-emphasizes via reduced contrast, but looks muddy on colored backgrounds. Hand-pick a same-hue color close to the background instead of using low-opacity white (which looks washed out or disabled).
- **Emphasize by de-emphasizing**: If an element won't "pop" no matter how you style it, de-emphasize its competing siblings or background instead.
- **Labels are a last resort**:
  - Format/context already conveys meaning (`$19.99`, `(555) 765-4321`, `user@domain.com`).
  - Combine labels and values: *"12 left in stock"* instead of *"In stock: 12"*; *"3 bedrooms"* instead of *"Bedrooms: 3"*.
  - When labels are necessary, style them as secondary — unless the page is an information-dense spec sheet where users actively scan for attribute names.
- **Separate visual hierarchy from document hierarchy**: Style headings by the visual prominence they need, not by their semantic HTML tag (`<h1>`–`<h6>`). Section titles are often supporting labels and should visually appear small.
- **Balance weight and contrast**: Solid icons and bold text cover more pixel surface area and read as heavy; balance them by lowering their contrast (`color: var(--slate-400)`). Conversely, boost the weight (not the darkness) of faint 1px borders by increasing width to 2px with a soft color.
- **Accessible Double-Ring Focus States**:
  ```css
  :focus-visible {
    outline: none;
    box-shadow: 0 0 0 2px var(--bg-surface), 0 0 0 4px var(--text-heading);
  }
  ```
- **The Action Pyramid**:
  - **Primary Action (1 per screen)**: Solid, high-contrast keycap with top light highlight.
  - **Secondary Actions (1-2 per screen)**: Tactile keycap with medium contrast.
  - **Tertiary Actions**: Discoverable but unobtrusive ghost buttons or text links.
  - **Destructive Actions**: Do NOT make every delete button big and red. Use secondary/tertiary styling on initial views, and reserve bold red styling for confirmation modals where deletion is the primary intent.

---

## 3. Layout, Spacing & Container Defense

- **White space should be *removed*, not incrementally added**: Default to more white space than feels natural, then subtract space until balanced. Dense UIs (dashboards) are a deliberate exception, not a fallback.
- **The 25% Rule**: No two adjacent values in your spacing scale should be closer than ~25% apart (`8px -> 12px -> 16px -> 24px -> 32px -> 48px -> 64px`).
- **Avoid Ambiguous Spacing**:
  - The gap between a label and its input must be significantly smaller than the gap to the next form group.
  - Spacing *above* a section heading must be greater than spacing *below* it.
  - Spacing *around* a component group must always exceed spacing *within* it.
- **Don't fill the whole screen**: If a form or card reads best at 500px–600px, constrain it with `max-width` and let whitespace frame it.
- **Treat 12-column grids as a tool, not a religion**: Give fixed elements (e.g. a 260px sidebar) a fixed width and let flexible content fill the rest. Don't force components to shrink or grow purely because of fluid grid percentages.
- **Relative sizing doesn't scale across breakpoints**: Large headline text must shrink faster than body copy on mobile. Size elements independently for each context instead of tying them to static `em` ratios.
- **Defensive Container Overflow**: Inside flex children with text truncation, always set `min-width: 0;` to prevent text from blowing out the container:
  ```css
  .flex-child-truncated {
    min-width: 0;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
  }
  ```

---

## 4. Typography & Modern Text Engineering

| Property | Tactical Rule / Heuristic |
| :--- | :--- |
| **Type Scale** | Hand-crafted px/rem scale (`12, 14, 16, 18, 20, 24, 30, 36, 48, 60px`). Avoid raw `em` in nested components to prevent fractional drift. |
| **Font Selection** | Favor neutral UI sans-serifs with 5+ weights + monospaced pair (`Plus Jakarta Sans` / `Inter` + `JetBrains Mono`). |
| **Line Length** | Maintain **45 to 75 characters per line** (~`20em` to `35em` width) for optimal reading rhythm. |
| **Alignment** | Left-align body copy (LTR). Reserve centering for short blocks ($\le 2-3$ lines). Right-align numeric tabular data so decimals align. |
| **Tabular Figures** | Always enable `font-variant-numeric: tabular-nums;` on prices, timers, metrics, and tables to eliminate jitter when numbers update. |
| **Text Wrapping** | Use `text-wrap: balance;` on headlines to prevent orphan words; use `text-wrap: pretty;` on paragraphs to avoid trailing single-word lines. |
| **Line Height** | **Inversely proportional to font size**: Tall line-height (`1.5`–`1.75`) for small body text; tight line-height (`1.0`–`1.2`) for large headings. |
| **Letter Spacing** | Slightly tighten (`-0.02em` to `-0.035em`) large headline font tracking. Widen (`+0.05em` to `+0.1em`) all-caps labels for legibility. |
| **Font Smoothing** | Always apply `-webkit-font-smoothing: antialiased; -moz-osx-font-smoothing: grayscale;` for crisp, subpixel rendering. |

---

## 5. Dual-Theme Color Systems, Materials & Tokens

- **Prefer HSL / OKLCH over Hex/RGB**: Map lightness and hue systematically to maintain consistent perceived contrast across light and dark modes.
- **3-Tier Semantic Token Architecture**:
  1. *Primitives*: `--slate-900`, `--slate-100`, `--emerald-500`.
  2. *Semantic Tokens*: `--bg-base`, `--bg-surface-1`, `--text-heading`, `--border-hairline`.
  3. *Component Tokens*: `--btn-primary-bg: var(--btn-pri-bg-top)`.
- **Dual-Theme Token Strategy**:
  ```css
  :root, [data-theme="dark"] {
    --bg-base: #09090b;
    --bg-surface-1: #121215;
    --bg-surface-2: #18181c;
    --text-heading: #ececf1; /* Titanium off-white */
    --text-primary: #d4d4d8;
    --border-hairline: rgba(255, 255, 255, 0.08);
    --border-top-highlight: rgba(255, 255, 255, 0.18);
    --border-bot-occlusion: rgba(0, 0, 0, 0.25); /* Soft, non-harsh */
  }
  [data-theme="light"] {
    --bg-base: hsl(220, 14%, 93.5%);
    --bg-surface-1: #ffffff;
    --bg-surface-2: hsl(220, 14%, 96.5%);
    --text-heading: #09090b;
    --text-primary: #27272a;
    --border-hairline: rgba(15, 23, 42, 0.08);
    --border-top-highlight: #ffffff;
    --border-bot-occlusion: hsl(220, 13%, 74%);
  }
  ```
- **Hue Rotation for Perceived Brightness**:
  - **To lighten without washing out**: Rotate hue toward the nearest bright peak (**Yellow 60°, Cyan 180°, Magenta 300°**).
  - **To darken with rich saturation**: Rotate hue toward the nearest dark valley (**Red 0°, Green 120°, Blue 240°**).
  - *Keep hue rotations within $20^\circ-30^\circ$ max.*
- **Translucent Frosted Materials**:
  - Use `backdrop-filter: blur(16px) saturate(180%)` with a hairline bottom border for sticky headers and floating command bars.

---

## 6. Subtle 3D Depth, Overhead Light Physics & Occlusion Tuning

In the physical world, light comes from directly above. Simulating an overhead ambient light source gives interfaces a tangible, high-craft feel without skeuomorphic clutter.

```
                   ☀️ OVERHEAD LIGHT SOURCE
                              │
                              ▼
  ┌────────────────────────────────────────────────────────┐  ▲ 1. Top Edge Bevel (Direct Light):
  │ ░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░ │     inset 0 1px 0 0 [Highlight] / border-top: 1px solid
  │                                                        │  ▲ 2. Face Micro-Gradient (Illumination Shift):
  │               BUTTON OR CARD BODY                      │     linear-gradient(180deg, #ffffff to #ebecef)
  │                                                        │
  ├────────────────────────────────────────────────────────┤  ▼ Recessed Shelf / Impact Card (Lower Z-Plane):
  │ [Impact Key: 14 jobs]   [Impact Value: 120s timeout]   │    inset 0 1px 2px rgba(0,0,0,0.15)
  └────────────────────────────────────────────────────────┘  ▼ 3. Bottom Occlusion Lip (Soft Shadow):
   ▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓     border-bottom: 1px solid [soft shade]
                                                              ▲ 4. Dual-Layer Shadow (Contact + Ambient):
                                                                 0 1px 2px (contact) + 0 4px 16px (ambient)
```

### The 4-Layer Lighting Model for Raised Elements

1. **Top Edge Bevel Highlight**:
   - **Dark Mode**: Translucent white 1px highlight (`border-top: 1px solid rgba(255, 255, 255, 0.18)`).
   - **Light Mode**: Pure white top bevel (`border-top: 1px solid #ffffff; box-shadow: inset 0 1px 0 #ffffff;`) made visible by tinting the upper card/button face ($2\%-3\%$ slope).
2. **Directional Face Micro-Gradient**:
   - Keep it subtle ($2\%-3\%$ delta): `linear-gradient(180deg, var(--bg-surface-2) 0%, var(--bg-surface-1) 100%)`.
3. **Bottom Lip Occlusion Calibration (Anti-Harshness Rule)**:
   - **Never make the dark mode bottom border pitch black (`#000` / `0.6 opacity`)** — it reads as a heavy dark stripe.
   - Calibrate to soft occlusion (`rgba(0, 0, 0, 0.25)`) or a subtle titanium tone (`#d1d5db` on white keycaps, `#9f1239` on red buttons).
4. **Dual-Layer Elevation Shadows**:
   - Combine a sharp, tight contact shadow with a softer ambient spread:
   ```css
   box-shadow: 
     inset 0 1px 0 0 var(--border-top-highlight),
     0 1px 2px 0 rgba(0, 0, 0, 0.25),
     0 4px 16px -2px rgba(0, 0, 0, 0.2);
   ```

### 🏎️ Fluid Motion & Physical Compression
- **Apple/Emil Spring Transition**:
  ```css
  --ease-spring: cubic-bezier(0.16, 1, 0.3, 1);
  --duration-fast: 120ms;
  transition: all var(--duration-fast) var(--ease-spring);
  ```
- **Hover Lift**: Sits up by `transform: translateY(-1px)` with an expanded ambient shadow.
- **Active Click Compression**: Element physically sinks into the plane: `transform: scale(0.985) translateY(1px);` with top cavity inset shadow.

---

## 7. Working with Images, Avatars & Icons

- **Use only high-quality photography**: A mediocre photo drags the whole design down.
- **Guaranteed contrast over hero images**:
  1. Semi-transparent dark/light scrim overlay.
  2. Lower image contrast and adjust brightness.
  3. Monochromatic colorization using CSS `mix-blend-mode: multiply`.
  4. Zero-offset diffuse text glow (`text-shadow: 0 0 20px rgba(0,0,0,0.8)`).
- **Intended Icon Size**: Never blow up 16–24px SVG icons to 64px. Enclose small icons inside a styled circular or rounded container (`36px`–`48px` badge) with a soft background tint.
- **Defensive User-Uploaded Content**:
  - Always set fixed aspect ratio containers with `object-fit: cover`.
  - Add an inner border (`inset 0 0 0 1px rgba(0,0,0,0.08)`) so white photos don't merge into light backgrounds.
  - Add an "invisible border" (`box-shadow: 0 0 0 2px var(--bg-surface)`) around overlapping avatar clusters to prevent collision.

---

## 8. High-Craft Components & Anti-Slop Patterns

| Component | High-Craft Implementation Strategy |
| :--- | :--- |
| **Confirmation Dialogs** | High z-axis elevation (`0 25px 50px -12px rgba(0,0,0,0.7)`), clean editorial title + status pill tag (`[CRITICAL]`), no cartoon warning triangles. |
| **Impact Callouts** | Replace 3px left-border stripes with structured inset trays (`.dialog-impact-card`) with monospaced key-value parameters. |
| **Segmented Controls** | Inset recessed tray containing a sliding active keycap with subtle top highlight and spring ease. |
| **Command Bar Trigger** | Recessed search trigger with monospaced keyboard badge (`⌘K`) and instant focus state. |
| **Flush Data Tables** | De-normalized multi-line cells (primary title + commit metadata), right-aligned `tabular-nums` metrics, and subtle row hover highlights. |
| **Live Status Indicators** | Status pulse badges (`rgba(16, 185, 129, 0.12)`) with glowing live heartbeat dots (`box-shadow: 0 0 8px currentColor`). |

---

## 9. Pre-Flight UI Audit Checklist

When reviewing or building any UI, verify against these checks:
- [ ] **Anti-Slop check**: Are there zero 3px colored left-border stripes, neon blobs, or cartoon warning triangle badges?
- [ ] **Anti-Glare check**: In dark mode, is display text titanium off-white (`#ececf1`) rather than stark `#ffffff`?
- [ ] **Dark mode bottom occlusion**: Are dark mode button bottom borders calibrated (`rgba(0,0,0,0.25)`) rather than harsh pitch black?
- [ ] **Lighting check**: Do raised elements have 1px top bevel highlights, subtle face gradients ($<3\%$), and contact shadows?
- [ ] **Light mode white element check**: Do white cards/buttons have subtle face tinting so the top white bevel highlight is visible?
- [ ] **Interaction & Motion check**: Do interactive elements depress (`scale(0.985) translateY(1px)`) with fast spring transitions (`< 120ms`)?
- [ ] **Action hierarchy check**: Is there a single distinct primary CTA per screen?
- [ ] **Focus check**: Are keyboard focus rings accessible double-rings (`box-shadow: 0 0 0 2px var(--bg), 0 0 0 4px var(--text-heading)`)?
- [ ] **Corner radius nesting**: Do inner corners satisfy $R_{\text{inner}} = R_{\text{outer}} - \text{padding}$?
- [ ] **Typography check**: Are numbers set to `tabular-nums`, headlines tightened (`-0.03em`), and paragraphs balanced?
