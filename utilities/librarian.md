---
name: loupe:librarian
description: Generate or update Loupe's design system reference file (library.md) by scanning component source files, tokens, typography, and icons. Use when the user says "/loupe:librarian", "generate library reference", "update library reference", "scan design system", or needs to create/maintain the library.md reference that Loupe agents consume.
---

# Loupe Librarian

Scans a project's design system source files and produces a structured, machine-readable reference file for Loupe agent consumption. Two modes: **generate** (from scratch) and **update** (surgical diff + patch).

**Output schema:** @~/.claude/skills/loupe/references/librarian-output-schema.md

## Modes

```
/loupe:librarian generate  — Full scan, produce new reference file
/loupe:librarian update    — Diff source against existing reference, patch changes
/loupe:librarian           — Prompt user to choose mode
```

## Inputs

Both modes require source paths. Prompt for any that aren't provided:

| Input | Required | Example |
|---|---|---|
| Components directory | Yes | `./src/components/FauxDS` |
| Token definitions | Yes | `./src/styles/tokens.ts` or `./src/styles/variables.css` |
| Icon definitions | No | `./src/components/SlateIcons.tsx` |
| Output path | Yes | `~/.claude/skills/loupe/references/library.md` |
| Figma token export | No | `./tokens.json` |

For update mode, also require: **path to existing reference file** (can be same as output path).

If the user provides no arguments at all, check if a `.librarian.json` config exists in the project root with saved paths from a previous run. If found, confirm and reuse. If not, prompt for each input.

## Generate Mode

### Step 1: Scan Components

For each file in the components directory:

1. Extract component name and export (named export, default export)
2. Extract all props from TypeScript interfaces/types or PropTypes
3. For each prop: name, type, required/optional, default value
4. Identify variant/size enums from union types or constant objects
5. Identify states from conditional styling (disabled, active, selected, error, loading)
6. Extract or generate a minimal usage example
7. Note gotchas: TODO/HACK comments, workarounds, props that don't behave as typed

**Subagent dispatch:** Use parallel Explore agents — one per component file or small batch — to extract component data. Collect results before assembling.

### Step 2: Scan Tokens

For each token/variable definition file:

1. Extract variable name (CSS custom property or JS/TS constant)
2. Extract resolved value (follow references if needed: `var(--x)` → actual value)
3. Group by category: colors, spacing, radii, shadows, typography, z-index, layout
4. If Tailwind config exists, map tokens to Tailwind utility classes
5. Identify token hierarchy: primitive → semantic mappings

**Multiple formats supported:** CSS custom properties, SCSS variables, JS/TS token objects, JSON token files, Tailwind config.

### Step 3: Scan Typography

Look for named text styles in:
- Dedicated typography files (typography.ts, text-styles.css)
- Component files for Text/Heading/Typography components
- Token files with typography-related variables
- Storybook stories or style guides

Extract per preset: name, font family, size, weight, line-height, letter-spacing.

### Step 4: Scan Icons

For each icon source:

1. Extract icon names (named exports, component names, SVG file names)
2. Note import path for each
3. Group by category if source organizes them (e.g., navigation, actions, status)
4. Identify external icon libraries from package.json dependencies
5. For external libraries: scan codebase for actual imports to list only icons in use

### Step 5: Identify Common Patterns

Scan the codebase for recurring composition patterns:

- Input field styling (border, padding, focus ring treatment)
- Dropdown/Select structure (trigger, portal, outside-click)
- Modal composition (portal, backdrop, content, close behavior)
- Form layouts (label + input + error message grouping)
- Card/list patterns
- Wrapper or utility components used 3+ times

Extract one minimal code snippet per pattern.

### Step 6: Assemble Reference File

Write the output following @references/output-schema.md:

1. File header with metadata (timestamp, source paths, counts)
2. Global Theme section
3. Components section (one entry per component, consistent shape)
4. Design Tokens section (grouped tables)
5. Typography Presets section (table)
6. Icons section (built-in + external)
7. Common Patterns section (code snippets)

### Step 7: Save Config

Write a `.librarian.json` to the project root with the paths used:

```json
{
  "componentsDir": "./src/components/FauxDS",
  "tokenPaths": ["./src/styles/tokens.ts"],
  "iconPaths": ["./src/components/SlateIcons.tsx"],
  "outputPath": "~/.claude/skills/loupe/references/library.md",
  "lastGenerated": "2026-03-26T12:00:00Z"
}
```

This enables `/librarian update` to run without re-prompting for paths.

### Output

Print summary:

```
Librarian: Reference generated
  Components: 12
  Tokens: 47
  Typography presets: 7
  Icons: 23 built-in, 8 external
  Patterns: 4
  Output: ~/.claude/skills/loupe/references/library.md
```

---

## Update Mode

### Step 1: Load Existing Reference

Read the existing reference file. Parse into structured sections:
- Components (name → full entry)
- Tokens (variable → value + category)
- Typography (preset → properties)
- Icons (name → source)
- Patterns (name → snippet)

If no existing reference file found at the path, offer to run generate mode instead.

### Step 2: Re-scan Source

Run the same scan logic as generate mode (Steps 1-5). Store results in memory — do not write anything yet.

### Step 3: Diff

Compare scanned results against existing reference:

| Category | Check |
|---|---|
| Components | New, removed, modified (props/variants/types changed) |
| Tokens | New, removed, value changed, category changed |
| Typography | New, removed, properties changed |
| Icons | New, removed |
| Patterns | New, removed, changed |

### Step 4: Present Diff

Display a structured summary:

```
Librarian Update Summary
========================
Components:
  + NewComponent (added)
  ~ Button: added "destructive" variant, changed default size "md" → "sm"
  - DeprecatedWidget (removed from source)

Tokens:
  + --color-accent-secondary: #7C3AED (new)
  ~ --color-grey-100: #F5F5F5 → #F7F7F7 (value changed)

Typography:
  ~ Caption: line-height 16px → 18px

Icons:
  + ArrowUpRight, ChevronDown (new)

No changes: Spacing, Shadows, Common Patterns
```

If the diff is empty: print "No changes detected. Reference is up to date." and exit.

### Step 5: Apply Updates

**If user passed `--yes` or confirms:** apply changes surgically.

- **New entries:** Insert in correct section, maintaining alphabetical order
- **Modified entries:** Replace only the changed entry, preserve unchanged entries verbatim
- **Removed entries:** Do NOT delete. Mark with `[DEPRECATED]` tag:

```markdown
## DeprecatedWidget [DEPRECATED]

[Original entry preserved]
```

After applying, offer: "Clean up [N] deprecated entries? (y/n)" — if yes, remove them entirely.

### Step 6: Update Metadata

Update the file header:
- `Last updated:` timestamp
- Revised component/token/icon counts
- Add `Changes:` line summarizing what was modified

Update `.librarian.json` with new timestamp.

---

## Key Behaviors

1. **Generate is full. Update is surgical.** Generate overwrites the entire file. Update only touches changed sections.
2. **Deprecation before deletion.** Removed components get `[DEPRECATED]`, not deleted. User confirms cleanup.
3. **Output is for machines.** Structured, terse, consistent. Every component entry has the same shape. Every token table has the same columns.
4. **Source code is truth.** Read actual TypeScript types, actual CSS variables, actual component files. Never rely on potentially-stale docs.
5. **Output path is configurable.** Default for Loupe: `~/.claude/skills/loupe/references/library.md`. Accept any path.
6. **Config persists.** `.librarian.json` saves paths so update mode doesn't re-prompt.

## Error Handling

| Condition | Action |
|---|---|
| Components directory doesn't exist | Error: "Components directory not found at [path]" |
| Token file doesn't exist | Error: "Token file not found at [path]" |
| No components found in directory | Warning: "No components found. Check the path." Continue with other sections. |
| No tokens found | Warning: "No token definitions found." Continue with other sections. |
| Existing reference malformed (update mode) | Warning: "Could not parse existing reference. Run generate mode to rebuild." |
| Output directory doesn't exist | Create it |
