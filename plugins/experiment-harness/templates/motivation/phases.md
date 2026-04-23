# Phases

Ordered. One phase per invocation per arm.

## 1. scaffold

Goal: produce `index.html` with the full semantic structure and all final copy (or near-final copy) written. Styling is not required yet — a single unstyled page is the right output.

Definition of done for this phase:
- `index.html` exists at the worktree root.
- All sections listed in `task.md` are present, with real written content (not placeholder or lorem ipsum).
- Semantic elements used where appropriate.
- Images referenced (via `<img>` tags) have `alt` attributes, even if they point to missing files.

## 2. style

Goal: add `styles.css`, linked from `index.html`, and arrive at a designed page. Typography, color, spacing, hierarchy, and layout all belong here. HTML may be lightly adjusted to support styling needs (wrapper divs, classes), but no new content should be added.

Definition of done:
- `styles.css` exists and is linked.
- The page renders with clear visual hierarchy at 1200px.
- Colors, type, and spacing make deliberate, coherent choices (not defaults).

## 3. polish

Goal: refine. This is the pass where responsive adjustments, accessibility tightening, microtypography, hover/focus states, small delights, or any final restraint go in.

Definition of done:
- The page renders acceptably at 375px (mobile) and 1200px (desktop).
- Focus states visible for interactive elements.
- Any lingering issues from earlier phases are resolved.
