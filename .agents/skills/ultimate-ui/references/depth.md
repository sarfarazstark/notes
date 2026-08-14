# ☀️ 3D Physical Lighting & Depth Model

Simulate an overhead ambient light source to give raised and inset elements tangible volume without skeuomorphic clutter.

```
                   ☀️ OVERHEAD LIGHT SOURCE
                              │
                              ▼
  ┌────────────────────────────────────────────────────────┐  ▲ 1. Top Edge Bevel (Direct Light):
  │ ░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░ │     inset 0 1px 0 0 [Highlight] / border-top: 1px solid
  │                                                        │  ▲ 2. Face Micro-Gradient (Illumination Shift):
  │               BUTTON OR CARD BODY                      │     linear-gradient(180deg, #ffffff to #ebecef)
  │                                                        │
  ├────────────────────────────────────────────────────────┤  ▼ Recessed Shelf / Inset Tray (Lower Z-Plane):
  │ [Impact Key: 14 jobs]   [Impact Value: 120s timeout]   │    inset 0 1px 2px rgba(0,0,0,0.15)
  └────────────────────────────────────────────────────────┘  ▼ 3. Bottom Occlusion Lip (Soft Shadow):
   ▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓     border-bottom: 1px solid [soft shade]
                                                              ▲ 4. Dual-Layer Shadow (Contact + Ambient):
                                                                 0 1px 2px (contact) + 0 4px 16px (ambient)
```

## The 4-Layer Lighting Model (For Raised Cards & Buttons)

1. **Top Edge Bevel Highlight**:
   - **Dark Mode**: Translucent white 1px highlight (`border-top: 1px solid rgba(255, 255, 255, 0.18)`).
   - **Light Mode**: Pure white top bevel (`border-top: 1px solid #ffffff; box-shadow: inset 0 1px 0 #ffffff;`) made visible by tinting the upper card/button face ($2\%-3\%$ off-white slope).
2. **Directional Face Micro-Gradient**:
   - Subtle ($2\%-3\%$ delta): `linear-gradient(180deg, var(--bg-surface-2) 0%, var(--bg-surface-1) 100%)`.
3. **Bottom Lip Occlusion Calibration (Anti-Harshness Rule)**:
   - **Never make dark mode bottom borders pitch black (`#000` / `0.6 opacity`)** — it reads as a heavy dark stripe.
   - Calibrate to soft occlusion (`rgba(0, 0, 0, 0.25)`) or a subtle titanium tone (`#d1d5db` on white keycaps, `#9f1239` on red buttons).
4. **Dual-Layer Elevation Shadows**:
   ```css
   box-shadow: 
     inset 0 1px 0 0 var(--border-top-highlight),
     0 1px 2px 0 rgba(0, 0, 0, 0.25),
     0 4px 16px -2px rgba(0, 0, 0, 0.2);
   ```

## Fluid Motion & Interaction States
- **Apple/Emil Spring Transition**:
  ```css
  --ease-spring: cubic-bezier(0.16, 1, 0.3, 1);
  --duration-fast: 120ms;
  transition: all var(--duration-fast) var(--ease-spring);
  ```
- **Hover Lift**: Sits up by `transform: translateY(-1px)` with an expanded ambient shadow.
- **Active Click Compression**: Element physically sinks into the plane: `transform: scale(0.985) translateY(1px);` with top cavity inset shadow.
