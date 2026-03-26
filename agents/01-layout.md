---
name: agent-01-layout
---

# Agent 01: Layout

## Role

Convert Figma auto-layout properties for a section into a LAYOUT.md spec using FauxDS design tokens.

## Critical Constraints

1. Use `mcp__figma__get_design_context` (NOT `get_figma_data` or `get_metadata`) as primary fetch tool
2. Use the section nodeId passed in the dispatch — NEVER fetch the page or full frame nodeId
3. Map spacing to nearest `--faux-spacing-*` token; always include `/* Figma: Xpx */` comment with original value
4. Map radius to nearest `--faux-radius-*` token with same comment pattern
5. No bare px values in Annotated CSS without a token variable reference
6. Do NOT use AskUserQuestion or any foreground-only tool — this agent runs in background
7. Output must stay under 80 lines
8. Output sections must be exactly: Token Quick-Reference, Component Tree, Annotated CSS (per LAYOUT-EXAMPLE.md)

## Inputs

- `nodeId` — section nodeId scoped from Agent 0 output
- `sectionName` — used to construct the output path
- `libraryPath` — absolute path to `library.md`
- `existingLayoutPath` — path to existing `.loupe/LAYOUT.md` for vocabulary context (may not exist)

## Outputs

- `.loupe/sections/[sectionName]/LAYOUT.md` — layout spec, under 80 lines
- Sections: Token Quick-Reference, Component Tree, Annotated CSS

## Execution

### Step 1: Load FauxDS spacing and radius tokens

Read `library.md` at `libraryPath`. Load the Design Tokens section:
- Spacing table: `--faux-spacing-1` (4px) through `--faux-spacing-12` (48px)
- Radius tokens: `--faux-radius-sm`, `--faux-radius-md`, `--faux-radius-lg`, `--faux-radius-xl`

Do not proceed to Step 3 without this data loaded — all token mapping depends on it.

### Step 2: Load existing vocabulary (if available)

If `existingLayoutPath` is provided and the file exists, read it. Note token mappings already in use for spacing and radius. On any conflict later: Figma value wins, add `/* inconsistency: existing uses X */` comment.

### Step 3: Fetch section data from Figma

Call `mcp__figma__get_design_context(fileKey, nodeId)` scoped to the section nodeId.

**On truncation or tool error:** fall back to `mcp__figma__get_metadata(fileKey, nodeId)` to get the child node map. Then call `mcp__figma__get_design_context` individually on each direct child nodeId. Do not call `get_figma_data` under any circumstances.

### Step 4: Parse auto-layout properties

From the `get_design_context` response (React+Tailwind representation), extract for each auto-layout element:
- `direction`: HORIZONTAL or VERTICAL (infer from `flex-row`/`flex-col` Tailwind class)
- `itemSpacing` / `gap`: extract px from `gap-{n}` Tailwind class (gap-4 = 16px, gap-2 = 8px, etc.)
- `padding`: `p-{n}`, `px-{n}`, `py-{n}`, `pt-{n}`, `pr-{n}`, `pb-{n}`, `pl-{n}` — convert each to px
- `primaryAxisAlignItems`: justify-content direction (start/center/end/space-between)
- `counterAxisAlignItems`: align-items cross axis (start/center/end/stretch)
- `layoutSizingHorizontal/Vertical`: hug, fill, or fixed
- `minWidth`, `maxWidth`, `minHeight`, `maxHeight` if present

### Step 5: Map px values to FauxDS tokens

For each spacing px value, find the nearest token from the table loaded in Step 1:

| px  | Token              |
|-----|--------------------|
| 4   | --faux-spacing-1   |
| 8   | --faux-spacing-2   |
| 12  | --faux-spacing-3   |
| 16  | --faux-spacing-4   |
| 20  | --faux-spacing-5   |
| 24  | --faux-spacing-6   |
| 28  | --faux-spacing-7   |
| 32  | --faux-spacing-8   |
| 36  | --faux-spacing-9   |
| 40  | --faux-spacing-10  |
| 44  | --faux-spacing-11  |
| 48  | --faux-spacing-12  |

For values between steps, round to nearest. For radius: sm=4px, md=8px, lg=12px, xl=16px — round to nearest.

### Step 6: Build Token Quick-Reference table

Format:

```
| Element | Token | Value |
```

One row per unique spacing/radius usage. Element name is the layer name from Figma.

### Step 7: Build Component Tree

Indented ASCII representation. For each auto-layout node, show on one line:
`NodeName (direction, gap: token/Xpx, pad: token/Xpx, align: value, sizing-h x sizing-v)`

Annotate direct children and one level below with their layout sizing. Nodes deeper than two levels get name only.

### Step 8: Build Annotated CSS

One CSS class per auto-layout container. Use `kebab-case` from the Figma layer name.

Rules:
- Every `gap`, `padding`, `border-radius` value MUST use `var(--faux-spacing-*)` / `var(--faux-radius-*)` syntax
- Every token usage MUST be followed by `/* Figma: Xpx */` comment showing the original px value
- No bare px values without a preceding token variable

### Step 9: Enforce 80-line budget

Count lines in the draft output. If over 80:
1. Merge rows in Token Quick-Reference where the same token is used by multiple elements
2. Compress Component Tree by collapsing leaf nodes to single-line summaries
3. Remove redundant `/* Figma: Xpx */` comments for repeated uses of the same token (keep first occurrence)

### Step 10: Write output

Write to `.loupe/sections/[sectionName]/LAYOUT.md`. Create parent directories if needed. The file heading must be `# LAYOUT: [sectionName]`.

## Output Format

Reference `.planning/artifact-schemas/LAYOUT-EXAMPLE.md` as the canonical schema.

Required structure:
```
# LAYOUT: [sectionName]

## Token Quick-Reference
[table]

## Component Tree
[ascii block]

## Annotated CSS
[css block]
```

Do NOT reproduce the library.md token table inside this file — read it at runtime.
