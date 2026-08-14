# 🧩 Component Patterns & Structures

## 1. Action Pyramid & Ergonomic Targets
- **Primary Action (1 per view)**: Solid, prominent keycap with top light highlight.
- **Secondary Actions (1-2 per view)**: Tactile keycap with medium contrast.
- **Tertiary Actions**: Unobtrusive ghost buttons or text links.
- **Destructive Actions**: Use secondary/tertiary styling on normal views; reserve bold red styling for confirmation modals where deletion is the primary intent.
- **Minimum Target Ergonomics (Apple HIG)**:
  - Desktop pointer targets: Minimum `32px × 32px` (e.g., `.btn-sm`).
  - Mobile touch targets: Minimum `44px × 44px` to prevent accidental mis-clicks.

## 2. High-Elevation Confirmation Dialogs
- High z-axis elevation (`0 25px 50px -12px rgba(0,0,0,0.7)`).
- Clean editorial header + status pill tag (`[CRITICAL]`).
- Structured impact parameter tray (`.dialog-impact-card`) with key-value pairs instead of paragraph blocks or 3px left-border stripes.

## 3. Flush Data Tables
- De-normalized multi-line cells (primary title + commit metadata).
- Right-aligned `tabular-nums` metrics.
- Subtle row hover highlights (`background: var(--bg-surface-hover)`).

## 4. Segmented Controls
- Recessed tray containing a sliding active keycap with subtle top highlight and spring ease.

## 5. Zero Layout Shift (Emil Design Eng)
- Reserve space for loading skeletons and async content so the container dimensions do not jump when data resolves.
