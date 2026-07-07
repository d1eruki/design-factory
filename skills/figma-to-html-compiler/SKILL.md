---
name: figma-to-html-compiler
description: Use when Codex must generate or update a 1:1 HTML/CSS site from a Figma frame using Figma API/raw JSON, deterministic IR, absolute positioning, exported assets, and numerical DOM geometry verification. Trigger for requests like pixel-perfect Figma to HTML, exact landing page generation from Figma, Figma JSON to static site, or fixing agent-made UI drift against a Figma file.
---

# Figma to HTML Compiler

Use this skill to avoid hand-recreated UI. Treat the task as a compiler pipeline: Figma source data → raw JSON artifact → normalized IR → absolute HTML/CSS/assets → Playwright geometry report.

## Hard Rule

Do not recreate the design by eye. Do not infer visual values from screenshots or preview images. Every generated coordinate, size, color, text style, asset, and layer order must come from Figma data, normalized IR, an explicit transformation rule, or a mapping manifest.

## Default Path

Use the `absolute-frame` workflow by default for 1:1 HTML sites and landing pages.

1. Require a Figma `fileKey` and source `nodeId`/frame id.
2. Require live Figma API/plugin access, a raw Figma JSON artifact, or a normalized IR artifact.
3. Fetch or load raw Figma JSON and save it as an artifact before generation.
4. Normalize the selected frame into Figma IR.
5. Export required image/vector assets from Figma.
6. Generate a fixed-size root frame and absolutely positioned child elements with `data-figma-id`.
7. Run Playwright verification against IR geometry and computed styles.
8. Report exact node deltas. Do not claim pixel-perfect output if verification did not pass.

## Source Decision

- For live Figma access and API/token details, read `references/source-contract.md`.
- For absolute HTML/CSS generation rules, read `references/absolute-html.md`.
- For numerical verification requirements, read `references/verification.md`.

If only a screenshot, public preview, or unresolved Figma URL is available, state that deterministic generation is blocked and ask for Figma API access, raw JSON, or IR.

## Required Output Contract

When performing a task with this skill, report:

- source mode: live Figma, raw JSON, or IR;
- source `fileKey` and `nodeId` when available;
- generated raw JSON and IR paths;
- generated HTML/CSS/assets paths;
- verification command;
- pass/fail result;
- remaining mismatches with node ids, expected values, actual values, and deltas.
