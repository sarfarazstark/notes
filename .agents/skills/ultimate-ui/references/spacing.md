# 📐 Spacing & Layout Architecture

## The 25% Non-Linear Spacing Scale
Values should sit ~25% apart: `4, 8, 12, 16, 24, 32, 48, 64, 96, 128px`.

## Layout & Container Defense
1. **White Space Subtraction**: Default to generous white space, then subtract until balanced.
2. **Proximity & Grouping**:
   - Gap between a label and its input must be tighter than the gap to the next group.
   - Spacing *within* a component must always be smaller than spacing *around* it.
3. **Flex Truncation Defense**:
   Inside flex containers with truncated text, **ALWAYS** set:
   ```css
   .flex-child-truncated {
     min-width: 0;
     overflow: hidden;
     text-overflow: ellipsis;
     white-space: nowrap;
   }
   ```
