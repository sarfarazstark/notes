# 📦 UI Library Selection & Primitive Architecture

When selecting or integrating a UI component library, ensure it allows complete ownership over styling, tokens, and physical lighting without fighting closed third-party stylesheets.

---

## 🎯 Selection Heuristics

### 1. Web Applications (React / Next.js / Vite)
- **Primary Choice**: **Headless Primitives + Owned Code** (e.g., `shadcn/ui`, Radix UI Primitives, React Aria Components).
  - *Why*: Gives 100% control over CSS tokens, nested radii, and 3D lighting without black-box overrides.
- **Avoid**: Monolithic locked-in component libraries (MUI, Chakra, Ant Design) where applying custom physical lighting or subtle bevels requires fighting CSS specificity and runtime emotion wrappers.

### 2. Mobile Applications (React Native / Expo)
- **Primary Choice**: **Tailwind/Native Primitives** (`HeroUI Native`, `React Native Reusables` / `react-native-primitives`).
  - *Why*: Direct access to native gesture systems, safe area context, and utility styling.

---

## 🛠️ Component State Matrix (MANDATORY per Primitive)

Every interactive primitive (Button, Input, Toggle, Card, Dialog) must explicitly define these states:

| State | Visual Requirement |
| :--- | :--- |
| **Default** | Resting 3D elevation (top bevel highlight, face micro-gradient, soft bottom occlusion). |
| **Hover** | Lift `-1px` (`translateY(-1px)`) with expanded ambient shadow. |
| **Focus-Visible** | Accessible double-ring focus (`0 0 0 2px var(--bg), 0 0 0 4px var(--text)`). Never hide focus without replacement. |
| **Active / Pressed** | Sink `+1px` (`scale(0.985) translateY(1px)`) + top cavity inset shadow. |
| **Disabled** | Reduced opacity (`0.5`), `cursor: not-allowed`, pointer events disabled, no active/hover transforms. |
| **Loading** | Inline spinner or shimmer with disabled pointer interactions; **maintain container dimensions** to prevent layout shift. |
