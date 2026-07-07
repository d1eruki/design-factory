# Verification Contract

The pass/fail mechanism must be numerical, not visual.

## Required Verification

Use Playwright to open the generated page and collect DOM data for every generated element with `data-figma-id`.

Collect geometry:

- `x`;
- `y`;
- `width`;
- `height`.

Collect computed styles where applicable:

- color;
- background color;
- border color;
- border width;
- border radius;
- opacity;
- font family;
- font size;
- font weight;
- line height;
- letter spacing.

Compare all actual values against the Figma IR.

## Default Tolerance

- `0px` for absolute-positioned frame/container coordinates after browser rounding;
- `<= 0.5px` for subpixel DOM geometry;
- `<= 1px` for text bounds if the same font files are used;
- `0px` for normalized RGBA colors;
- `<= 0.01` for opacity;
- `0px` for asset intrinsic dimensions.

Looser tolerance requires an explicit reason in the report.

## Failure Behavior

Fail loudly when:

- Figma data cannot be fetched;
- a required node is missing from IR;
- a mapped DOM element is missing;
- geometry exceeds tolerance;
- a font is missing;
- an asset export fails;
- a Figma feature is unsupported and no fallback is defined.

Each failure must include:

```text
figmaId: <node-id>
field: <x|y|width|height|style>
expected: <value from IR>
actual: <value from DOM>
delta: <difference>
tolerance: <allowed tolerance>
```

Screenshots may be saved as debug artifacts, but they are not the authority.
