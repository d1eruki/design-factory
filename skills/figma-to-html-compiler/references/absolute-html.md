# Absolute HTML Workflow

Use this workflow when the user asks for a 1:1 HTML site from a Figma frame.

## Core Model

The selected Figma frame becomes a fixed coordinate system. Child nodes are generated from normalized Figma bounds.

```text
.frame {
  position: relative;
  width: <frame-width>px;
  height: <frame-height>px;
}

[data-figma-id="12:34"] {
  position: absolute;
  left: <x-relative-to-frame>px;
  top: <y-relative-to-frame>px;
  width: <width>px;
  height: <height>px;
}
```

## IR Requirements

For each supported node, preserve:

- `id`, `name`, `type`;
- parent id and child order;
- `absoluteBoundingBox`;
- local bounds relative to the selected frame;
- `absoluteRenderBounds` when available;
- rotation, opacity, clipping, visibility;
- fills, strokes, effects, corner radii;
- text characters and text style;
- font family, size, weight, line height, letter spacing, alignment;
- export metadata for image/vector nodes.

## Generation Rules

- Generate `data-figma-id` on every element that maps to a Figma node.
- Preserve Figma layer order deterministically.
- Use exact text from Figma unless a manifest marks the text as dynamic.
- Use local font files when possible. Report missing fonts as a verification risk.
- Export complex vectors/images from Figma instead of redrawing them manually.
- Do not substitute approximated CSS for Figma effects unless the transformation is documented.
- Keep generated output isolated from hand-written project code where possible.

## Responsive Behavior

Do not invent responsive behavior from a single desktop frame. If the user needs responsive output, require separate Figma frames or explicit constraints for each breakpoint.

Acceptable responsive strategy:

- generate one absolute frame per breakpoint;
- switch frames with CSS media queries;
- verify each breakpoint against its matching Figma IR.

## Known Risk Areas

- font rendering can differ by browser/OS;
- Figma effects may not map exactly to CSS;
- blend modes and masks can require raster/SVG export;
- browser subpixel rounding can create `0.5px` deltas;
- responsive layout cannot be inferred safely from one frame.
