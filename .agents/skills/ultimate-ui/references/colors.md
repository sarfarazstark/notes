# 🎨 Dual-Theme Design Token Architecture

## Token Layering
1. **Primitives**: `--slate-900`, `--slate-100`, `--emerald-500`.
2. **Semantic Tokens**: `--bg-base`, `--bg-surface-1`, `--text-heading`, `--border-hairline`.
3. **Component Tokens**: `--btn-primary-bg: var(--btn-pri-bg-top)`.

## Dual-Theme Template
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

## Frosted Glass Materials
Use `backdrop-filter: blur(16px) saturate(180%);` with a hairline bottom border for sticky headers and floating command bars.
