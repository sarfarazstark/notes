---
name: refactoring-ui
description: Apply a hierarchy-first, system-driven UI design and refactoring methodology to web or app interfaces. Use when designing a new UI, reviewing an existing screen, polishing components, fixing visual inconsistency, or refactoring frontend styling. Focus on information hierarchy, constrained design systems, spacing, typography, color, depth, imagery, empty states, and deliberate visual decisions rather than blindly adding decoration.
---

# Refactoring UI

Use this skill to make interfaces feel intentional, readable, consistent, and polished through design decisions that can be explained and repeated.

## Core philosophy

Treat an interface as a collection of user-facing features, not as a shell that must be designed all at once.

Prefer this loop:

1. Identify the concrete feature or user task being improved.
2. Build the smallest useful version of the interface.
3. Establish hierarchy before decoration.
4. Introduce constrained design systems instead of arbitrary values.
5. Implement the real UI early.
6. Inspect the working result and iterate.
7. Repeat for the next feature.

Do not over-design future functionality or edge cases that are not ready to build. Prefer the smallest shippable version and add nice-to-have detail later.

## 1. Establish the visual direction

Before making detailed styling decisions, decide what personality the interface should communicate.

Consider:
- Typography and font family.
- Primary and accent colors.
- Border-radius language.
- Tone of interface copy.
- The audience and the conventions of products they already use.

Pick a direction and stay consistent. Do not mix unrelated corner radii, type personalities, or visual languages without a clear reason.

Limit choices aggressively. Create reusable systems for values such as:
- Font sizes and weights.
- Line heights.
- Spacing and dimensions.
- Colors and shades.
- Border radii and widths.
- Shadows/elevation.
- Opacity.

When a decision repeats, turn it into a system instead of choosing a new arbitrary value.

## 2. Hierarchy is the primary design tool

Start every visual critique by asking: what should the user notice first, second, and third?

Do not make everything loud. A polished interface usually has a clear hierarchy:
- Primary content/action: strongest emphasis.
- Secondary content/action: clear but quieter.
- Tertiary content/action: discoverable but unobtrusive.

Use a combination of:
- Size.
- Font weight.
- Contrast.
- Color.
- Position.
- Whitespace.
- Surface treatment.

Do not rely on font size alone. Avoid making primary content unnecessarily huge or secondary content unreadably small. Prefer reducing the contrast of supporting information rather than shrinking it excessively.

Two font weights are generally enough for most UI work: a normal weight for most content and a heavier weight for emphasis. Avoid very light text in application interfaces.

When something does not stand out enough, first look for competing elements that can be de-emphasized. Often the best way to emphasize one thing is to quiet everything around it.

### Labels and data

Do not default to `label: value` for every piece of information.

Prefer:
- Letting recognizable formats speak for themselves.
- Combining the label and value into natural language when possible.
- Treating labels as supporting text when labels are required.

For information-dense UIs, emphasize whichever side of the label/value pair the user is actually scanning for.

### Semantics vs visual hierarchy

Use semantic HTML and accessible structure, but do not let an element's semantic tag dictate its visual size.

Choose `h1`, `h2`, etc. for document/accessibility structure; style them according to the interface's visual hierarchy.

Buttons and links should also reflect importance. Semantics do not mean every action deserves equal visual weight.

For destructive actions, severity does not automatically make the action the primary action. If deletion is secondary, give it a secondary/tertiary treatment and reserve strong destructive emphasis for the confirmation step.

## 3. Layout and spacing

Start with more whitespace than you think you need, then remove excess. It is usually easier to notice when whitespace is excessive than when there is too little.

Dense layouts are appropriate when information density is the actual product requirement, such as operational dashboards. Density should be intentional, not accidental.

### Use a spacing and sizing system

Avoid arbitrary near-values such as 120px vs 125px unless the difference is meaningful.

Use a constrained scale with noticeably different adjacent steps. As a practical heuristic, adjacent values should differ by roughly 25% or more where possible so each choice is meaningfully distinct.

Do not blindly force every value to be a multiple of 4. The goal is useful perceptual spacing, not mathematical purity.

### Do not fill the canvas just because space exists

Use the width the content actually needs.

Prefer `max-width` and intrinsic/fixed dimensions where appropriate. Do not stretch narrow forms, text blocks, sidebars, or controls merely to occupy available space.

Design mobile constraints early. A narrow viewport is often a useful constraint for discovering the right hierarchy and content width before expanding the design.

### Use grids as a tool, not a religion

Do not force every component into percentage-based grid widths.

Use fixed or intrinsic widths for elements that should not scale with the viewport. Let the main flexible region absorb remaining space.

Use `max-width` to prevent components from becoming unnecessarily wide, and only force them to shrink when space genuinely becomes constrained.

### Avoid proportional-scaling dogma

Do not assume every property must scale in lockstep with another property.

Large elements often need to shrink faster than small elements on smaller screens. Typography, padding, dimensions, and gaps can each have their own responsive behavior.

Relative units can be useful, but preserve the intended visual result rather than preserving a mathematical ratio.

### Avoid ambiguous spacing

Spacing communicates grouping.

Use more space between groups than within groups so relationships are obvious without requiring borders or labels.

For forms, lists, cards, and horizontal control groups, ensure internal spacing is tighter than the spacing separating distinct groups.

## 4. Typography

Define a type scale instead of inventing font sizes ad hoc.

For UI work, a hand-crafted scale is often more practical than a mathematically perfect modular scale. Prefer rounded values and a limited set of sizes.

Keep the implementation tied to the defined scale. Prefer `px` or `rem` for explicit type-system values; avoid nested `em` scaling when it causes computed sizes to drift away from the intended scale.

Use legible, well-crafted fonts. A neutral sans-serif is a safe default for product UI when there is no strong brand requirement.

Keep body text line length around 45-75 characters per line. For web layouts, a content width around 20-35em is a useful starting point, then tune by eye.

When mixing font sizes on the same line, align text by baseline rather than vertical centering.

Choose line-height based on font size and line length:
- Smaller text generally needs more leading.
- Larger headlines can use tighter leading.
- Wider paragraphs generally need more line-height than narrow paragraphs.

Do not center long-form text. Center short headlines or compact independent blocks when appropriate. Right-align numeric columns in tables for easy comparison.

Treat letter-spacing as a subtle tool:
- Tighten large headlines when the typeface benefits from it.
- Add tracking to all-caps labels when needed for legibility.
- Otherwise trust the typeface defaults.

## 5. Color systems

Think in color roles and families, not isolated hex values.

HSL is useful for reasoning about hue, saturation, and lightness when designing web palettes, though the implementation format should follow the project's existing conventions.

A practical product palette generally needs:
- A useful neutral/grey scale.
- One or two primary brand color families.
- A small set of semantic/accent families such as success, warning, error, or informational colors.

Define shades up front. Prefer a fixed scale such as 100-900 or an equivalent token system instead of dynamically inventing slightly different shades throughout the UI.

Do not depend on generic `lighten()` / `darken()` transformations for every state. Hand-tune important shades so they remain visually coherent.

Neutral colors can be subtly warm or cool. A grey does not have to be literal zero-saturation grey.

### Accessibility and contrast

Check text contrast rather than assuming a color is accessible.

When light text on a saturated background forces the background to become too dark and visually dominant, consider flipping the contrast: use a tinted/light surface with darker colored text.

For colored text on colored surfaces, changing hue can sometimes create separation without making the text nearly white.

Never use color as the only semantic signal. Pair it with icons, text, shape, position, contrast, or another redundant cue.

Do not use low-contrast grey text on colored backgrounds as a generic de-emphasis trick. Choose a color that is intentionally related to the background hue and has the right contrast.

## 6. Depth and elevation

Use a consistent virtual light source. For most interfaces, imagine light coming from above.

Raised surfaces can use:
- A subtle lighter top edge/highlight.
- A restrained downward shadow.

Inset surfaces can use the inverse relationship.

Avoid photorealistic effects. Depth should clarify hierarchy and interaction, not become decoration for its own sake.

### Elevation system

Define a small set of reusable elevation levels rather than one-off shadows. A handful of levels is usually enough.

Use elevation semantically:
- Low: buttons or slightly lifted controls.
- Medium: popovers/dropdowns.
- High: dialogs/modals.

When interaction changes perceived elevation, use the same system. A pressed button may reduce its shadow; a draggable item may gain elevation while being dragged.

Two-part shadows can be useful when you need a subtle large ambient shadow plus a tighter contact shadow. Keep the contact shadow less prominent at higher elevations.

Depth can exist without shadows. Lighter surfaces, darker inset surfaces, solid offset shadows, and overlapping layers can all communicate depth.

## 7. Imagery

Use high-quality photography or purposeful illustrations. Do not expect placeholder imagery to validate a final design.

Text over images needs stable contrast. If a background image creates inconsistent readability:
- Add an overlay.
- Reduce image contrast and adjust brightness.
- Colorize/desaturate the image.
- Use a subtle text shadow when appropriate.

Choose imagery at the intended display size. Do not casually scale tiny icons up or detailed screenshots down until their content becomes inappropriate for the target size.

For user-uploaded images, control the presentation shape and size with fixed containers and cropping. Prevent background bleed with subtle inner shadows or low-contrast inner borders rather than harsh outlines.

## 8. Finishing touches

Polish existing elements before adding new ones.

Useful finishing techniques include:
- Replacing generic bullets with meaningful icons.
- Styling active/selected states deliberately.
- Adding restrained accent borders.
- Using subtle background changes or gradients.
- Adding low-contrast decorative patterns or shapes.
- Reworking links, checkboxes, radios, and other browser-default controls when the product's design language calls for it.

### Empty states

Treat empty states as first-class UX, especially for user-generated content.

An empty state should explain what is happening and provide a clear next action. Hide supporting controls that are meaningless until content exists.

### Borders

Use fewer borders. Before adding a border, ask whether the same separation can be achieved with:
- Spacing.
- Surface/background contrast.
- Elevation/shadow.

Borders are valid, but they should not become the default separator for every region.

### Break conventions intentionally

Common components do not have a single mandatory visual form.

A dropdown can contain sections, icons, columns, or supporting context. A table can combine related information. Radio choices can become selectable cards.

Do this when it improves hierarchy, comprehension, or product personality—not simply to be different.

## 9. Refactoring workflow for an existing UI

When asked to refactor an existing screen or component, follow this order:

### Step 1 — Understand the feature

Identify:
- The user goal.
- The primary action.
- Supporting actions.
- Critical information.
- Secondary and tertiary information.
- Empty/loading/error states.
- Responsive constraints.

Do not start by changing CSS randomly.

### Step 2 — Audit hierarchy

Check whether the current UI makes it obvious:
1. What matters most?
2. What should be read next?
3. What can safely fade into the background?

Look for visual competition from:
- Too many bold elements.
- Too many colors.
- Excessive borders.
- Large headings.
- High-contrast secondary controls.
- Icons that are visually heavier than adjacent text.

### Step 3 — Normalize systems

Identify arbitrary values and map them to the project's existing tokens.

Look for duplicated one-off values for:
- Spacing.
- Font sizes.
- Font weights.
- Colors.
- Border radii.
- Shadows.
- Widths/heights.

Prefer existing design tokens over introducing new tokens.

### Step 4 — Fix geometry and grouping

Adjust:
- Container widths.
- Max-widths.
- Gaps.
- Padding.
- Group separation.
- Alignment.
- Responsive behavior.

Start with too much whitespace, then reduce it.

### Step 5 — Fix typography

Check:
- Type scale.
- Weight hierarchy.
- Line length.
- Line-height.
- Baseline alignment.
- Heading proportions.
- Letter-spacing where relevant.

### Step 6 — Fix color

Check:
- Neutral hierarchy.
- Primary/accent usage.
- Contrast.
- Semantic states.
- Color-only communication.

Do not add more colors merely because the screen feels boring; first determine whether hierarchy and spacing are doing enough work.

### Step 7 — Add depth and finishing touches

Only after hierarchy, geometry, typography, and color are working should you add:
- Shadows/elevation.
- Accent borders.
- Background decoration.
- Refined control states.
- Purposeful illustrations/icons.

### Step 8 — Inspect all states

Verify at minimum:
- Normal/content-rich state.
- Empty state.
- Loading state, where applicable.
- Error state, where applicable.
- Disabled state, where applicable.
- Hover/focus/pressed states for interactive elements.
- Narrow/mobile viewport.
- Wide desktop viewport.

### Step 9 — Final visual audit

Ask:
- Is there one obvious primary action?
- Is secondary content quiet enough?
- Are related elements grouped clearly by spacing?
- Are widths constrained to the content's needs?
- Are values coming from reusable systems?
- Are there unnecessary borders, shadows, or colors?
- Is text comfortable to read?
- Does the UI still work when real content is longer or missing?
- Is the personality consistent?

When reviewing an existing design, actively look for decisions that feel unconventional but effective. Strong UI often contains small choices that would not have been the first choice of an inexperienced designer.

## Anti-patterns to reject

Do not:
- Design the full app shell before understanding real features.
- Polish shadows/icons/colors before the layout and hierarchy work.
- Invent arbitrary pixel values throughout a screen.
- Make everything bold, large, or high contrast.
- Use grey text on colored backgrounds by default.
- Use color as the only semantic indicator.
- Make every component full-width.
- Force everything into percentage-based grid columns.
- Preserve rigid proportional relationships across responsive states when they hurt the design.
- Center long-form text.
- Use borders as the default separator everywhere.
- Build elaborate empty states only after the rest of the UI is finished.
- Add speculative UI for features that are not ready to ship.

## Adaptation rules

This is a design methodology, not a fixed visual theme. Adapt tokens, exact values, components, and patterns to the product's existing brand and platform.

Preserve the principles:
- Hierarchy before decoration.
- Systems before arbitrary values.
- Whitespace and grouping before borders.
- Readability before visual cleverness.
- Consistency before novelty.
- Smallest useful feature before speculative complexity.
- Implement early, inspect often, and iterate.
