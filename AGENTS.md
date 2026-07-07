# Agent Instructions

This repository is for deterministic Figma-to-code work. Do not recreate UI by eye. Do not infer visual values from screenshots, previews, or subjective interpretation.

## Required Skill

For 1:1 Figma-to-HTML tasks, use the bundled skill:

```text
skills/figma-to-html-compiler/
```

This skill is the authoritative workflow for this repository. It defines the source contract, absolute HTML generation rules, and numerical verification requirements.

The local Codex skills directory should point to the repository version:

```text
~/.codex/skills/figma-to-html-compiler
-> <repo>/skills/figma-to-html-compiler
```

## Repository Policy

Every generated visual value must come from one of these sources:

- raw Figma API/plugin data;
- normalized Figma IR;
- an explicit transformation rule documented in code;
- a mapping manifest.

If a value cannot be traced to one of those sources, do not present it as exact.

## Required Workflow

Use this pipeline for pixel-accurate output:

```text
Figma API or raw JSON
-> raw JSON artifact
-> normalized IR
-> exported assets
-> absolute HTML/CSS
-> Playwright geometry verification
```

The default mode is `absolute-frame`: the selected Figma frame becomes a fixed coordinate system, and child nodes are generated as absolutely positioned DOM elements with `data-figma-id`.

## Required Inputs

Do not start deterministic generation without:

- Figma `fileKey`;
- source `nodeId` or frame id;
- live Figma API/plugin access, raw Figma JSON, or normalized IR;
- target output path;
- tolerance profile.

If only a screenshot, public preview, unresolved Figma URL, or verbal description is available, stop and ask for machine-readable Figma data.

## Verification Rule

The pass/fail mechanism must be numerical. Use Playwright to compare DOM `getBoundingClientRect()` and computed styles against Figma IR.

Screenshots may be saved for debugging, but they are not the source of truth.

Do not claim pixel-perfect output unless geometry verification passed or the exact unverified gaps are reported.

## Work Format

Before changing code, state:

- source mode: live Figma, raw JSON, or IR;
- source `fileKey` and `nodeId` when available;
- generation mode;
- expected raw JSON, IR, HTML/CSS, and asset paths;
- tolerance profile.

After changing code, report:

- files changed;
- generated artifacts;
- verification command;
- pass/fail result;
- remaining mismatches with node ids and deltas.
