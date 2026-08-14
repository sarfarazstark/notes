# 🔤 Typography, Optical Tracking & Anti-Glare System

## Rules & Standards

1. **Anti-Glare in Dark Mode (Level 2 - SHOULD)**:
   - ❌ **Never use pure stark `#ffffff` for dark mode text** — it produces harsh visual glare and eye fatigue.
   - **Display Headings**: Titanium off-white (`#ececf1`).
   - **Primary Body**: Soft slate / zinc (`#d4d4d8`).
   - **Secondary Labels**: Medium zinc (`#9ca3af` / `#a1a1aa`).
   - **Tertiary Metadata**: Muted gray (`#6b7280` / `#71717a`).

2. **Apple Optical Tracking Curve (Level 2 - SHOULD)**:
   - **Display Headings ($> 20\text{px}$)**: Tighten tracking slightly (`-0.02em` to `-0.035em`) to pull large letterforms together.
   - **Body Text ($14\text{px}-18\text{px}$)**: Standard default tracking (`0em`).
   - **Small Uppercase Labels ($\le 12\text{px}$)**: Widen tracking (`+0.05em` to `+0.1em`) with uppercase transforms for scannability.

3. **Tabular Numerics (Contextual - Level 3)**:
   - Apply `font-variant-numeric: tabular-nums;` to metrics, counters, timers, prices, and tables to eliminate jitter when numbers update.

4. **Text Wrapping Heuristics**:
   - `text-wrap: balance;` on headlines to prevent single trailing orphan words.
   - `text-wrap: pretty;` on paragraphs to avoid single trailing words.

5. **Type Scale & Sizing Scale**:
   - Hand-crafted px/rem scale: `12, 14, 16, 18, 20, 24, 30, 36, 48, 60px`.
   - Line length: 45–75 characters per line (~`20em`–`35em`).
   - Subpixel rendering: Always apply `-webkit-font-smoothing: antialiased; -moz-osx-font-smoothing: grayscale;`.
