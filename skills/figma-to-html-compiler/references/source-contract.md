# Source Contract

The generator needs machine-readable Figma data. A visual reference is not enough.

## Accepted Source Modes

### Live Figma Source

Use when Codex or the tool can access Figma directly.

Required:

- Figma `fileKey`;
- source `nodeId` or frame id;
- Figma API token or Figma plugin execution context;
- permission to export image/vector assets;
- Figma file version metadata when available.

Save raw Figma JSON before normalization. A typical artifact path is:

```text
artifacts/figma/raw/<run-id>.figma.json
```

### Frozen Raw JSON Source

Use when Figma data was exported earlier.

Required:

- raw Figma JSON artifact;
- source `nodeId` or frame id contained in that JSON;
- exported assets or permission to re-export assets;
- metadata identifying the Figma file/version that produced the JSON.

This is the best mode for reproducible tests because it does not depend on live Figma availability.

### Normalized IR Source

Use when extraction and normalization already happened.

Required:

- Figma IR artifact;
- asset manifest;
- mapping manifest when existing components are involved;
- tolerance profile.

This mode can regenerate code without reading Figma, but it must not claim to include newer Figma changes unless IR was regenerated.

## Insufficient Sources

These are not enough for deterministic 1:1 HTML:

- screenshot only;
- Figma preview image only;
- Figma URL without API/plugin access and without exported JSON;
- verbal design description;
- copied CSS without node ids and bounds.

If only insufficient sources exist, stop and ask for one accepted source mode. The task may continue as a prototype, but it must not be called verified, deterministic, or pixel-perfect.

## Minimum Practical Input

```text
figma fileKey + nodeId + API/plugin access
or
raw Figma JSON + nodeId + exported assets
or
Figma IR + asset manifest
```
