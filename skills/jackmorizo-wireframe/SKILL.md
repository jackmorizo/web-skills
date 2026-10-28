---
name: jackmorizo-wireframe
description: Create low-fidelity wireframe images of UI screens as SVG (light and dark mode), including multi-step interaction storyboards and annotated callouts. Use when asked for a wireframe, mockup sketch, screen layout, or "what happens when I click X".
---
# Wireframes

## Output rules
- Write hand-written SVG files to `wireframes/`. Each screen or interaction flow gets TWO files:
  - `wireframes/<name>.light.svg`
  - `wireframes/<name>.dark.svg`
- Do NOT write ASCII art, React, HTML, or any app code.

## PNG conversion (optional)
- Ask the user ONCE per session, before the first conversion: "Should I also convert the SVGs to PNG?" Remember the answer for the rest of the session, and don't ask again.
- If the answer is yes, check `command -v rsvg-convert`.
  - If it is available, convert each file with `rsvg-convert wireframes/X.light.svg -o wireframes/X.light.png`, and do the same for the dark file.
  - If it is not available, skip conversion. Mention once that rsvg-convert isn't installed, and don't try alternatives or install anything.
- Self-check: when a PNG exists, look at the light PNG once to check for overlaps, clipped text, or arrows crossing labels. Without a PNG, re-read the SVG coordinates instead. Fix only real problems; don't polish.

## Light and dark mode
All colors come from a `<style>` block at the top of the SVG. Never put color values in element attributes, so the two modes differ ONLY in that block.

1. Write the light file completely.
2. Create the dark file by copying the light file and replacing only the `<style>` block, for example with a small sed or Python script. NEVER regenerate the drawing for dark mode.

Use these classes:

```svg
<style>
  /* LIGHT */
  .bg     { fill: #ffffff; }
  .ui     { stroke: #000000; fill: none; stroke-width: 2; }
  .fill   { fill: #000000; }               /* label text */
  .muted  { stroke: #999999; fill: none; stroke-width: 2; }  /* placeholder text lines */
  .dim    { stroke: #cccccc; fill: none; stroke-width: 2; }  /* background behind modals */
  .blue   { stroke: #1971C2; fill: none; } .t-blue   { fill: #1971C2; }
  .orange { stroke: #F08C00; fill: none; } .t-orange { fill: #F08C00; }
  .green  { stroke: #2F9E44; fill: none; } .t-green  { fill: #2F9E44; }
  .red    { stroke: #E03131; fill: none; } .t-red    { fill: #E03131; }
  text    { font-family: sans-serif; }
</style>
```

```svg
<style>
  /* DARK */
  .bg     { fill: #1e1e1e; }
  .ui     { stroke: #e6e6e6; fill: none; stroke-width: 2; }
  .fill   { fill: #e6e6e6; }
  .muted  { stroke: #777777; fill: none; stroke-width: 2; }
  .dim    { stroke: #444444; fill: none; stroke-width: 2; }
  .blue   { stroke: #4DABF7; fill: none; } .t-blue   { fill: #4DABF7; }
  .orange { stroke: #FFA94D; fill: none; } .t-orange { fill: #FFA94D; }
  .green  { stroke: #69DB7C; fill: none; } .t-green  { fill: #69DB7C; }
  .red    { stroke: #FF6B6B; fill: none; } .t-red    { fill: #FF6B6B; }
  text    { font-family: sans-serif; }
</style>
```

The background is always the first element: `<rect class="bg" width="100%" height="100%"/>`.

## Canvas size
- One wireframe frame is AT LEAST 1280x800 (desktop) or 390x844 (mobile).
- Make it larger whenever the page needs it (long pages, wide tables, etc.).
- The overall SVG viewBox grows to fit all frames, the gutters between them, and the annotation margin (see below).

## Base style (low fidelity)
- Wireframe elements use `class="ui"`, and labels use `class="fill"`.
- Images are a box with an X through it.
- Body text is horizontal lines with `class="muted"`, not real paragraphs.
- Real labels only where they carry meaning: button text, headings, nav items, field labels.
- No icons, shadows, gradients, or decoration.
- Group elements with `<g id="header">`, `<g id="sidebar">` etc. so later edits are cheap and targeted.

## Accent colors
Use these sparingly, only to point out what matters. Everything else stays in the base colors.

| Class   | Meaning                                    |
|---------|--------------------------------------------|
| blue    | Primary action / the thing the user clicks |
| orange  | What changed or appeared after an action   |
| green   | Success state, new feature, confirmation   |
| red     | Errors, destructive actions, warnings      |

Typical uses are a colored outline (`class="blue" stroke-width="3"`) around an element, a dashed outline (`stroke-dasharray="8 6"`) around a newly appeared area, or the color of an annotation arrow and its note (text uses `t-<color>`).

## Interaction storyboards (multiple states in one SVG)
Use these when showing what happens on an action, such as a click opening a sidebar, modal, or dropdown.
- Place the frames left to right, with an 80–120px gutter between them. Use a grid for flows longer than 3 steps.
- Put a label above each frame: "1 · Default", "2 · After clicking 'Filter'", and so on.
- In the earlier frame, outline the element that triggers the change in blue.
- In the later frame, outline what is new or changed with an orange dashed outline. Keep the unchanged parts identical so the difference is obvious.
- Draw a curved arrow across the gutter from one frame to the next, with a short action label ("click", "hover", "submit").
- A modal sits on top of the existing frame, with the background drawn in `class="dim"` to suggest an overlay.

## Annotations with curved arrows
Use annotations when something needs explaining that doesn't fit inside the wireframe, such as a button that triggers several things.
- Reserve an annotation margin of about 320px to the right of the frames (or left or below, if that is closer to the targets). Notes live there, never on top of the UI.
- Number the notes ("①", "②" …) when there are several, and keep them roughly level with what they point at.
- SVG doesn't wrap text, so break notes into lines of about 40 characters with `<tspan x=".." dy="1.3em">`.
- The arrow goes from the note to the target element. It ends about 6px before the element's edge, and the element itself gets an outline in the same color.
- Arrows are ALWAYS curved and never straight. Use a cubic Bézier where the control points bow the line sideways, so it feels hand-drawn.
- An arrow must not cross notes or labels. If it would, bend it the other way or move the note.
- Each arrow's color matches its note and the meaning table above.

Define one arrowhead marker per color you use (its fill uses the `t-<color>` class, so it switches with the theme), and use this pattern:

```svg
<defs>
  <marker id="arrow-blue" viewBox="0 0 10 10" refX="9" refY="5"
          markerWidth="8" markerHeight="8" orient="auto-start-reverse">
    <path class="t-blue" d="M0 0 L10 5 L0 10 z"/>
  </marker>
  <!-- arrow-orange, arrow-green, arrow-red: same, different class -->
</defs>

<!-- note in the margin -->
<text class="t-blue" x="1340" y="210" font-size="15">
  <tspan x="1340" dy="0">① "Save" stores the draft,</tspan>
  <tspan x="1340" dy="1.3em">closes the sidebar and shows</tspan>
  <tspan x="1340" dy="1.3em">a success toast.</tspan>
</text>

<!-- curved arrow: start next to the note, bow outward, end near the target -->
<path class="blue" stroke-width="2"
      d="M 1330 215 C 1250 215, 1230 110, 1150 96"
      marker-end="url(#arrow-blue)"/>
```

For the Bézier `M x1 y1 C c1x c1y, c2x c2y, x2 y2`, put c1 roughly horizontal from the start and c2 offset toward the target. The result is a gentle S-curve or arc. Avoid control points that make loops.