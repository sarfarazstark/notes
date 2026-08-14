# Agent Skill Trigger Issue

**User:** Anonymous  
**Created:** 8/14/2026 23:37:51  
**Updated:** 8/14/2026 23:39:40  
**Exported:** 8/14/2026 23:40:02  
**Link:** [https://chatgpt.com/c/6a7f5951-24c0-83e8-b85a-eaa09e92df83](https://chatgpt.com/c/6a7f5951-24c0-83e8-b85a-eaa09e92df83)  

## Prompt:
8/14/2026, 11:37:49 PM

this skill doesnt work when i use it for agent

[Attachment: SKILL.md]

## Response:
8/14/2026, 11:37:51 PM

Yes. Looking at the `SKILL.md`, I can see why an agent may **not reliably trigger it**.

The biggest issue is that this file is written more like a **reference document + behavioral prompt** than a robust agent skill.

### The main problem

Your frontmatter says:

```yaml
name: ultimate-ui
description: >-
  Tactical UI/UX design heuristics...
  Use whenever designing, building, reviewing...
```

The description is doing too much. It contains both:

1. **What the skill is**
2. **When the agent should invoke it**

That can work in some skill systems, but agents that use semantic skill selection often need the description to be **short, explicit, and trigger-oriented**.

Your current description also starts with:

> "Tactical UI/UX design heuristics..." `SKILL.md`

That's good as a summary, but the actual trigger is buried after it.

---

## I'd restructure it like this

```yaml
---
name: ultimate-ui
description: >
  Use this skill whenever a task involves designing, building, styling,
  reviewing, or improving a UI. This includes web pages, dashboards,
  forms, tables, cards, buttons, navigation, typography, spacing,
  colors, shadows, responsive layouts, dark mode, or component styling.
  Apply the skill's UI heuristics during implementation and code review.
---

# Ultimate UI

Apply these rules when working on UI/UX.

## Core rules

### Visual hierarchy
- De-emphasize secondary information instead of making everything prominent.
- Use weight and contrast deliberately.
- Use 400/500 for normal UI text and 600/700 for emphasis.
- In dark mode, avoid pure white text; prefer `#ececf1` for headings.

### Spacing
Use the spacing scale:

`4, 8, 12, 16, 24, 32, 48, 64, 96, 128px`

- Label-to-input spacing should be tighter than spacing between form groups.
- Component-internal spacing should be tighter than spacing around the component.
- Constrain content when a narrower width improves readability.
- Use `min-width: 0` in flex children where text truncation is required.

### Typography
- Use a deliberate type scale.
- Prefer 45-75 characters per line for body text.
- Use `tabular-nums` for metrics and numeric tables.
- Use `text-wrap: balance` for headings.
- Use `text-wrap: pretty` for paragraphs.

### Color
- Prefer semantic design tokens.
- Separate primitive, semantic, and component tokens.
- Design light and dark themes as systems rather than independently chosen colors.

### Components
- Establish one clear primary action per screen.
- Secondary actions should have lower visual weight.
- Destructive actions should not dominate normal UI.
- Use consistent corner radii.
- Nested components should generally use smaller radii than their containers.

### Anti-slop
Do not introduce:
- Neon purple/blue glowing blobs
- Generic AI gradients
- 3px colored left-border alert stripes
- Cartoon warning icons in confirmation dialogs
- Excessive border usage
- Arbitrary inconsistent corner radii

### Depth
For raised elements:
- Use a subtle top highlight.
- Use a very subtle face gradient.
- Use contact + ambient shadows.
- Avoid harsh black borders.
- Use short spring-like interaction transitions.

### Interaction
- Hover: approximately `translateY(-1px)`
- Active: approximately `scale(0.985) translateY(1px)`
- Use fast transitions around 120ms.
- Provide accessible `:focus-visible` states.

## Decision process

Before implementing a UI:

1. Identify the primary user task.
2. Establish hierarchy.
3. Establish spacing.
4. Establish typography.
5. Establish the color/token system.
6. Apply component-specific styling.
7. Add depth and motion only where useful.
8. Review the result against the anti-slop checklist.

When principles conflict, explicitly prioritize the most important product constraint.
```

### Why this should work better

Your existing skill has the right **content**. For example, it already contains useful concrete rules such as the spacing scale, typography rules, action hierarchy, and anti-slop constraints. `SKILL.md` `SKILL.md`

The problem is more about **skill architecture and retrieval** than the actual UI knowledge.

I'd also avoid making the description enormous. The detailed rules belong in the body; the description should primarily answer:

> **"When should the agent load this skill?"**

---

### One more important thing

If by **"agent"** you mean a specific agent system such as **Claude Code, Gemini CLI, Qwen Code, Cursor, Codex, VS Code Agent, etc.**, the correct `SKILL.md` structure can differ significantly.

If you tell me **which agent you're putting this into**, I can adapt this exact skill to that agent's skill-loading mechanism rather than guessing.

## Prompt:
8/14/2026, 11:38:38 PM

even if i mention it to use, it doesnt apply the rules

## Response:
8/14/2026, 11:38:40 PM

Right - **that changes the diagnosis**.

If the agent loads the skill but still ignores the rules even when you explicitly say *“use the ultimate-ui skill”*, then the problem isn't primarily the trigger description. It's that the skill is functioning as **reference material**, rather than as **mandatory execution instructions**.

Your file currently says:

> “A condensed, production-grade decision framework...” `SKILL.md`

and then gives lots of heuristics. That's useful knowledge, but an agent can interpret it as:

> “Here is a collection of UI advice.”

instead of:

> “These are constraints I must actively enforce in my output.”

### The fix

You need to make the skill **operational**.

For example, instead of:

```md
## Typography

- Use a deliberate type scale.
- Prefer 45-75 characters per line.
- Use tabular-nums for metrics.
```

make it:

```md
## Typography - REQUIRED

When implementing UI:

1. Use the defined type scale.
2. Apply `font-variant-numeric: tabular-nums` to metrics,
   prices, timers, and tabular numerical data.
3. Apply `text-wrap: balance` to headings.
4. Apply `text-wrap: pretty` to paragraphs.
5. Do not introduce arbitrary font sizes unless the design
   requirement requires one.

Before completing the task, inspect the generated UI/code and
verify these rules were followed.
```

That distinction matters a lot.

---

## Your skill has another problem

You've got **255 lines of rules**.

That's actually too much for an agent to reliably apply simultaneously.

For example, you've got:

- anti-slop rules
- spacing system
- typography
- color theory
- HSL/OKLCH
- hue rotation
- dark mode
- physical lighting
- shadows
- motion
- images
- avatars
- dialogs
- segmented controls
- command bars
- tables
- status indicators
- audit checklist

All of that is useful, but an agent working on:

> "Build me a settings page"

doesn't necessarily need to reason about hue rotation, overhead lighting physics, avatar collision, etc.

So I'd split it into:

```text
ultimate-ui/
├── SKILL.md
├── rules/
│   ├── hierarchy.md
│   ├── spacing.md
│   ├── typography.md
│   ├── color.md
│   ├── components.md
│   ├── depth-motion.md
│   └── anti-slop.md
└── checklist.md
```

Then `SKILL.md` becomes the **controller**.

---

# More importantly: tell the agent HOW to use it

I'd put this near the very top:

```md
# Ultimate UI

## Mandatory execution policy

This is an **active implementation skill**, not a reference document.

When this skill is loaded, its rules are constraints on the UI you
produce.

For every UI task:

1. Identify which rules in this skill apply to the task.
2. Apply those rules during implementation, not only during review.
3. Do not ignore a rule merely because the user did not explicitly
   mention it.
4. Prefer the rules in this skill over generic UI conventions when
   they conflict.
5. Before finishing, perform the Pre-Flight UI Audit.
6. If an implementation violates a rule, fix it before returning
   the result.
7. Do not claim compliance without actually checking the output.

### Required final verification

Before completing any UI task, inspect the resulting implementation
against:

- visual hierarchy
- spacing
- typography
- color/token system
- component hierarchy
- anti-slop rules
- interaction states
- responsive behavior
- accessibility
- depth/shadow rules where applicable

Only rules relevant to the current UI need to be applied. Do not
force irrelevant rules into the design.
```

That is much stronger.

---

## And make the checklist actionable

Your existing checklist is currently phrased as questions:

> “Are there zero 3px colored left-border stripes...?” `SKILL.md`

I'd change it to something like:

```md
# Pre-Flight UI Audit

This audit is REQUIRED before completing a UI task.

### Hierarchy
- [ ] There is a clear primary action.
- [ ] Secondary information is visually subordinate.
- [ ] Competing elements have been deliberately de-emphasized.

### Spacing
- [ ] Spacing uses the defined spacing scale.
- [ ] Internal component spacing is tighter than external spacing.
- [ ] Related elements are visually grouped.

### Typography
- [ ] Font sizes come from the defined type scale.
- [ ] Headings have appropriate weight and contrast.
- [ ] Numeric data uses tabular figures where appropriate.
- [ ] Headings use balanced wrapping where appropriate.

### Color
- [ ] Colors come from semantic tokens.
- [ ] Dark-mode text does not use harsh pure white.
- [ ] Contrast is intentional.

### Components
- [ ] Corner radii are consistent.
- [ ] Primary/secondary/tertiary actions have distinct hierarchy.
- [ ] Destructive actions are not unnecessarily dominant.

### Anti-Slop
- [ ] No neon AI gradients/blobs.
- [ ] No 3px colored left-border alert stripes.
- [ ] No cartoon warning badges.
- [ ] No arbitrary border/radius combinations.

### Interaction
- [ ] Hover state exists where appropriate.
- [ ] Active/pressed state exists where appropriate.
- [ ] Focus-visible state is accessible.
- [ ] Motion is fast and subtle.

### Final
- [ ] I reviewed the actual implementation.
- [ ] I fixed violations found during the audit.
```

Now the agent has an explicit **procedure**, not just design philosophy.

---

### One thing I would *not* do

Don't add something like:

> **“ALWAYS FOLLOW EVERY RULE IN THIS FILE.”**

That sounds stronger to us, but it doesn't actually solve the agent's reasoning problem. It also encourages the model to blindly apply irrelevant rules.

The useful instruction is:

> **Identify applicable rules → implement them → audit the actual output → fix violations.**

That's the behavior you're trying to induce.

If you're using this with **Claude Code / Codex / Gemini CLI / Qwen Code**, tell me which one. The exact reason it ignores a loaded skill can be different depending on how that agent handles `SKILL.md`, and I can restructure this file specifically for that runtime.

## Prompt:
8/14/2026, 11:39:24 PM

Gemini Cli and Claude

## Response:
8/14/2026, 11:39:26 PM

> **Searching the web**
>
>
> **Searching 2 websites**
>
> 
>
> Worked for a few seconds

Ah - **Gemini CLI + Claude Code** explains it.

Your `SKILL.md` is valid in the general Agent Skills format, but you're trying to use a **design-reference skill as if it were an enforcement layer**. Both agents can load the skill without necessarily applying every heuristic to every UI decision.

Gemini explicitly describes the skill lifecycle as: metadata → activation → inject the `SKILL.md` body → agent proceeds using the specialized guidance. ([Gemini CLI](https://geminicli.com/docs/cli/skills/?utm_source=chatgpt.com)) Claude Code uses the same Agent Skills ecosystem, so the right approach is to make the skill's body highly procedural rather than encyclopedic.

### I would change your skill architecture

Your current file is essentially:

```text
SKILL.md
  ├── philosophy
  ├── heuristics
  ├── examples
  ├── CSS
  ├── lighting theory
  ├── component patterns
  └── checklist
```

Instead, make it:

```text
ultimate-ui/
├── SKILL.md                  ← behavioral controller
└── references/
    ├── hierarchy.md
    ├── spacing.md
    ├── typography.md
    ├── color.md
    ├── depth.md
    ├── components.md
    └── anti-slop.md
```

This follows Gemini's own recommendation: keep `SKILL.md` focused on **core procedural instructions** and move detailed reference material into `references/`. ([Gemini CLI](https://geminicli.com/docs/cli/skills-best-practices/?utm_source=chatgpt.com))

---

# The critical part

Your `SKILL.md` should start something like this:

```md
---
name: ultimate-ui
description: >
  Mandatory UI design and implementation standards. Use whenever creating,
  modifying, reviewing, or styling any user interface, including web pages,
  dashboards, forms, tables, cards, buttons, navigation, mobile interfaces,
  responsive layouts, typography, spacing, colors, shadows, and interactions.
---

# Ultimate UI

## PURPOSE

This is an **active implementation skill**.

It is NOT merely a reference document.

When this skill is active, you MUST use its rules when making UI decisions.

The rules apply to the UI you create or modify, even when the user does not
explicitly mention them.

---

# REQUIRED WORKFLOW

For EVERY UI task:

### Phase 1 - Analyze

Before writing UI code:

1. Identify the primary user task.
2. Identify the primary action.
3. Identify the information hierarchy.
4. Identify the relevant UI rules from this skill.
5. Determine whether the interface is dense, normal, or spacious.

Do not immediately start writing components before establishing this.

### Phase 2 - Implement

While implementing the UI:

1. Apply the relevant Ultimate UI rules.
2. Use the defined spacing system.
3. Use the defined typography hierarchy.
4. Use semantic color tokens.
5. Maintain consistent component geometry.
6. Apply interaction states.
7. Avoid the anti-slop patterns.
8. Do not introduce arbitrary values when an existing system value works.

### Phase 3 - Review

After implementation, inspect the ACTUAL resulting UI/code.

Check:

- hierarchy
- spacing
- typography
- colors
- component geometry
- responsive behavior
- interaction states
- accessibility
- anti-slop violations

### Phase 4 - Correct

If the review finds a violation:

**FIX IT before completing the task.**

Do not merely mention the violation.

Do not claim the design follows the skill unless you actually checked it.

---

# RULE PRIORITY

When making UI decisions, use this priority:

1. User's explicit functional requirements
2. Existing project's technical constraints
3. Existing project design system
4. Ultimate UI rules
5. Generic UI conventions

Do not violate a higher-priority requirement to satisfy this skill.

When no higher-priority constraint exists, follow Ultimate UI.

---

# NON-NEGOTIABLE UI RULES

## Visual hierarchy

- Every screen must have a clear primary action.
- Secondary information must be visually subordinate.
- Do not make every element visually prominent.
- De-emphasize competing elements before increasing the prominence of the target.

## Spacing

Use:

`4, 8, 12, 16, 24, 32, 48, 64, 96, 128px`

Prefer these values over arbitrary spacing.

Internal component spacing should generally be tighter than spacing
between component groups.

## Typography

Use:

`12, 14, 16, 18, 20, 24, 30, 36, 48px`

- Use 400/500 for normal UI text.
- Use 600/700 for emphasis.
- Use tabular figures for numerical data.
- Balance headings.
- Avoid unnecessary font-size variation.

## Anti-slop

DO NOT introduce:

- neon purple/blue glow effects
- generic AI gradients
- decorative glowing blobs
- 3px colored left-border alert stripes
- cartoon warning icons
- excessive borders
- arbitrary corner-radius combinations
- unnecessary glassmorphism
- decorative effects that do not communicate hierarchy

## Dark mode

Do not use pure `#ffffff` for primary dark-mode text.

Prefer:

- Heading: `#ececf1`
- Primary text: `#d4d4d8`
- Secondary: `#9ca3af`
- Tertiary: `#6b7280`

## Interaction

Interactive controls should have:

- hover state where appropriate
- active/pressed state
- focus-visible state
- disabled state where applicable

Use subtle motion rather than decorative animation.

---

# FINAL AUDIT

Before finishing any UI task, ask internally:

> "If I removed the user's explicit design instructions, would this
> implementation still demonstrate the principles defined by Ultimate UI?"

If the answer is no, review the implementation again.

Then fix any violations before returning the result.
```

That's the part your current skill is missing.

---

## Why this is better for Gemini

Gemini's documentation specifically says the description is what the model sees **before activation**, while the detailed body is injected only after activation. ([Gemini CLI](https://geminicli.com/docs/cli/skills-best-practices/?utm_source=chatgpt.com))

So you want:

**Description**

> "Use this whenever doing UI work."

Then once activated:

**SKILL.md**

> "Here is the exact procedure you MUST follow."

Then:

**references/**

> "Here is the detailed design knowledge you can consult."

That's progressive disclosure rather than dumping 250+ lines of design theory into the primary instruction layer.

---

# And there's an important Gemini test

Run:

```text
/skills list
```

and verify `ultimate-ui` appears.

Then:

```text
/skills reload
```

if you changed it during the session. Gemini explicitly supports this workflow. ([Gemini CLI](https://geminicli.com/docs/cli/tutorials/skills-getting-started/?utm_source=chatgpt.com))

Then give Gemini something deliberately simple:

> Build a settings page with a save button, form fields, and a destructive delete action. Use the ultimate-ui skill.

You should see the skill activate.

The interesting test is **not whether it activates**.

The test is whether it:

- uses your spacing scale
- creates a clear primary action
- avoids the anti-slop patterns
- uses the typography hierarchy
- implements focus states
- performs a final audit

If it activates but doesn't do those things, the issue is the **instruction design of the skill**, not skill discovery.

---

# For Claude Code

I'd use the **same skill directory** rather than maintaining separate Gemini and Claude versions.

That's one of the nice things about the `.agents/skills` convention: Gemini CLI explicitly supports `.agents/skills` as an interoperable alias for Agent Skills. ([Gemini CLI](https://geminicli.com/docs/cli/skills/?utm_source=chatgpt.com))

So I'd aim for:

```text
.agents/
└── skills/
    └── ultimate-ui/
        ├── SKILL.md
        └── references/
            ├── hierarchy.md
            ├── spacing.md
            ├── typography.md
            ├── color.md
            ├── components.md
            ├── depth.md
            └── anti-slop.md
```

That gives you one source of truth for **Gemini CLI + Claude Code**.

### But I would make one further change

Don't put **all 255 lines** into `references/` either.

Your current file contains some rules that are genuinely **core constraints**, such as:

> "Never use pure stark `#ffffff` for dark mode headings or body text" `SKILL.md`

and:

> "No Generic Tropes" / "No 3px Left-Border Stripes" `SKILL.md`

Those should remain in `SKILL.md`.

The detailed explanation of *why* those rules exist, lighting physics, examples, etc. should move out.

**In short:**

```text
SKILL.md
    ↓
"What must I do?"

references/
    ↓
"How exactly should I do it?"
```

That distinction is what I'd change in your skill.

---

**Sources:**

- [Agent Skills | Gemini CLI](https://geminicli.com/docs/cli/skills/?utm_source=chatgpt.com)



---
Powered by [ChatGPT Exporter](https://www.chatgptexporter.com)