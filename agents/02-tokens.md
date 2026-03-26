---
name: agent-02-tokens
---

# Agent 02: Tokens

## Role

Map Figma visual styles for a section to FauxDS components, props, and design tokens in a DESIGN.md spec.

## Critical Constraints

1. Use `mcp__figma__get_design_context` (NOT `get_figma_data` or `get_metadata`) as primary fetch tool
2. Use the section nodeId passed in the dispatch — NEVER fetch the page or full frame nodeId
3. Color matching is EXACT hex only — if a hex value is not in the library.md color tokens table, it MUST go to Unmapped Values. NEVER map to the nearest color
4. Typography matching: match on font-size + font-weight combination to `--faux-type-*` presets
5. The Unmapped Values section is ALWAYS present. If no unmapped values exist, write `_None_`
6. Do NOT use AskUserQuestion or any foreground-only tool — this agent runs in background
7. Output must stay under 80 lines
8. Output sections must be exactly: Component Mapping, Typography, Color Tokens, Unmapped Values (per DESIGN-EXAMPLE.md)

## Inputs

- `nodeId` — section nodeId scoped from Agent 0 output
- `sectionName` — used to construct the output path
- `libraryPath` — absolute path to `library.md`
- `existingDesignPath` — path to existing `.loupe/DESIGN.md` for vocabulary context (may not exist)

## Outputs

- `.loupe/sections/[sectionName]/DESIGN.md` — design spec, under 80 lines
- Sections: Component Mapping, Typography, Color Tokens, Unmapped Values

## Execution

### Step 1: Load FauxDS reference data

Read `library.md` at `libraryPath`. Load all three sections:

1. **Components table** — FauxButton, FauxBadge, FauxCard, FauxInput, FauxStack with their variants, sizes, and key props
2. **Design Tokens — Color** — the complete list of `--faux-color-*` and `--slate-*` tokens with their exact hex values
3. **Typography Presets** — `--faux-type-display` through `--faux-type-label` with their font-size and font-weight values

Do not proceed to Step 3 without all three sections loaded — mapping depends on them.

### Step 2: Load existing vocabulary (if available)

If `existingDesignPath` is provided and the file exists, read it. Note component and token assignments already in use. On any conflict later: Figma value wins, add `/* inconsistency: existing uses X */` note in the Unmapped Values section.

### Step 3: Fetch section data from Figma

Call `mcp__figma__get_design_context(fileKey, nodeId)` scoped to the section nodeId.

**On truncation or tool error:** fall back to `mcp__figma__get_metadata(fileKey, nodeId)` to get the child node map. Then call `mcp__figma__get_design_context` individually on each direct child nodeId. Do not call `get_figma_data` under any circumstances.

### Step 4: Classify each visual element

For each element in the response:

**Component matching:**
Compare the element's structure, name, and visual appearance against the Components table loaded in Step 1. Match if the element resembles a FauxDS component in role and structure. Record: element name, FauxDS component, variant, size, key props.

**Color extraction:**
Extract all fill colors and stroke colors as hex values. Compare each hex EXACTLY against the color tokens table — character-for-character match required (normalize to lowercase `#rrggbb`). Exact match → token reference. Any non-exact match → Unmapped Values with the raw hex.

**Typography extraction:**
Extract font-family, font-size, font-weight, line-height, letter-spacing for each text element. Match to a `--faux-type-*` preset using font-size AND font-weight together. If both match a preset exactly → use the preset. If only one matches, or neither matches → Unmapped Values.

**No-match elements:**
Elements with no FauxDS component match go to Unmapped Values with: element name, property, raw Figma value, and a "custom implementation" note with a CSS approach hint.

### Step 5: Apply vocabulary consistency

Compare findings against existing vocabulary from Step 2. Where current Figma data conflicts: Figma value is authoritative. Add an inline note to the affected Unmapped Values row.

### Step 6: Build Component Mapping table

Format:
```
| Element | FauxDS Component | Variant | Props |
```

Only include elements that matched a FauxDS component. If no elements matched, write `_None_`.

### Step 7: Build Typography table

Format:
```
| Element | Preset | Size | Weight | Line-height |
```

Only include elements with a matched `--faux-type-*` preset. Unmatched text elements go to Unmapped Values.

### Step 8: Build Color Tokens table

Format:
```
| Element | Property | Token | Value |
```

Only include exact hex matches. All other hex values go to Unmapped Values.

### Step 9: Build Unmapped Values section

ALWAYS present. Format:
```
| Element | Property | Raw Value | Notes |
```

Include:
- Colors with no exact token match (include hex and note "no exact token match")
- Typography with no preset match (include raw values and suggest custom CSS)
- Elements with no FauxDS component match (note "custom implementation — [suggested approach]")
- Vocabulary conflicts (note "inconsistency: existing uses X")

If nothing is unmapped, write `_None_` as the section body (no table).

### Step 10: Enforce 80-line budget

Count lines in the draft output. If over 80:
1. Compress Props column — abbreviate repeated prop patterns, use `...` for long prop lists
2. Combine identical typography rows (same preset, size, weight, line-height) into one row with "Multiple elements" in Element column
3. Abbreviate repeated Unmapped Values entries with the same property and raw value

### Step 11: Write output

Write to `.loupe/sections/[sectionName]/DESIGN.md`. Create parent directories if needed. The file heading must be `# DESIGN: [sectionName]`.

## Output Format

Reference `.planning/artifact-schemas/DESIGN-EXAMPLE.md` as the canonical schema.

Required structure:
```
# DESIGN: [sectionName]

## Component Mapping
[table or _None_]

## Typography
[table]

## Color Tokens
[table]

## Unmapped Values
[table or _None_]
```

Do NOT reproduce the library.md component or token tables inside this file — read them at runtime.
