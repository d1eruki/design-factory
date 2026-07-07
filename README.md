# Design Factory

A tool for applying repository changes from a Figma file without manually guessing sizes, spacing, colors, or layout.

## Goal

Build a pipeline where the Figma file is the source of truth and the repository is updated from structured data: coordinates, dimensions, styles, text, assets, and stable node identifiers.

This should not be an agent that looks at a design and recreates something "close enough". It should be a compiler: Figma → IR → code.

## Required Design Inputs

A Figma file link alone is not enough for deterministic generation unless the tool can fetch structured data from it.

For precise generation, the tool needs one of these input modes:

1. **Live Figma source**
   - Figma `fileKey`;
   - source `nodeId` or frame id;
   - Figma access token or plugin execution context;
   - permission to export image/vector assets.

2. **Frozen Figma source**
   - previously exported raw Figma JSON;
   - source `nodeId` or frame id inside that JSON;
   - exported assets or permission to re-export them;
   - metadata describing the Figma file version.

3. **Normalized IR source**
   - previously generated Figma IR;
   - asset manifest;
   - mapping manifest;
   - tolerance profile.

The most repeatable workflow is to store both raw Figma JSON and normalized IR as artifacts for every run. The raw JSON proves what came from Figma. The IR proves what the generator consumed.

Minimum practical input for pixel-accurate work:

```text
figma fileKey + nodeId + API/plugin access
or
raw Figma JSON + nodeId + exported assets
```

The tool must not generate pixel-claimed code from a screenshot, a public preview image, or a Figma link that cannot be resolved into structured node data.

## How to Achieve Maximum Precision

Absolute precision is only realistic when the scope is constrained:

1. Use a specific Figma frame as the fixed coordinate system.
2. Generate DOM/CSS from Figma coordinates, not from subjective visual interpretation.
3. Preserve `data-figma-id` on every generated DOM element.
4. Load the exact same font files locally, with the same weights.
5. Export complex vector/image layers from Figma instead of rebuilding them by hand in HTML/CSS.
6. Verify numerically: compare DOM `getBoundingClientRect()` values against Figma `absoluteBoundingBox` values.

For a fixed-viewport landing page, near pixel-perfect output is achievable with absolute positioning. For a responsive product, "pixel-perfect at every width" is impossible unless the Figma file provides explicit constraints, auto-layout rules, and breakpoint-specific frames.

## Recommended Architecture

```text
Figma API / Plugin
        |
        v
Figma Raw JSON
        |
        v
Normalizer
        |
        v
Figma IR
        |
        +--> tokens: colors, typography, radius, shadows
        +--> assets: svg, png, webp, fonts
        +--> layout: frames, nodes, bounds, constraints
        |
        v
Code Generator / Patcher
        |
        v
Project files
        |
        v
Geometry verifier
```

## Bundled Codex Skill

This repository includes a reusable Codex skill for the strict 1:1 workflow:

```text
skills/figma-to-html-compiler/
```

Use this skill when an agent must generate an exact HTML/CSS page from a Figma frame. The skill forces the workflow through raw Figma JSON, normalized IR, absolute-positioned HTML/CSS, exported assets, and numerical Playwright verification.

For local Codex usage, symlink the repository skill into the Codex skills directory:

```text
~/.codex/skills/figma-to-html-compiler
-> <repo>/skills/figma-to-html-compiler
```

In this workspace, the symlink points to:

```text
/Users/dieruki/Projects/design-factory/skills/figma-to-html-compiler
```

## Figma IR

The IR should be a stable JSON format independent of any particular frontend framework.

Minimal node example:

```json
{
  "id": "12:34",
  "name": "Hero title",
  "type": "TEXT",
  "bounds": { "x": 120, "y": 96, "width": 640, "height": 116 },
  "style": {
    "fontFamily": "Inter",
    "fontSize": 56,
    "fontWeight": 700,
    "lineHeight": 58,
    "color": "rgba(20, 22, 26, 1)"
  }
}
```

## Precision Verification

The primary test should compare numbers instead of screenshots:

```text
figma node 12:34
expected: x=120 y=96 width=640 height=116
actual:   x=120 y=96 width=640 height=116
delta:    x=0   y=0  width=0   height=0
```

Verify:

- coordinates and dimensions;
- border radius;
- fill/stroke colors;
- opacity;
- typography;
- asset dimensions;
- z-index/order where it affects layout.

## Practical Implementation Plan

1. Build an extractor that fetches Figma JSON by `fileKey` and `nodeId`.
2. Build a normalizer that converts Figma JSON into IR.
3. Build a manifest that maps Figma nodes to project files.
4. Build a generator for the selected frontend stack.
5. Build a Playwright verifier that compares DOM geometry against IR.
6. Build a CLI:

```bash
design-factory pull --file <figma-file-key> --node <node-id>
design-factory apply
design-factory verify
```

## Important Limitation

If the current codebase is a hand-written responsive layout and the Figma file only contains one desktop frame, exact matching on mobile/tablet cannot be guaranteed. Precision requires separate Figma frames for breakpoints or formal constraints.
