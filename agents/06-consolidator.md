---
name: agent-06-consolidator
---

# Agent 06: Consolidator

## Role

Merge all section-level LAYOUT.md and DESIGN.md artifacts from `.loupe/sections/` into consolidated project-level vocabulary files at `.loupe/LAYOUT.md` and `.loupe/DESIGN.md`. Extend existing files on subsequent runs — never replace them. Clean up the staging directory only after both consolidated files are verified.

## Critical Constraints

1. **Extend, never overwrite** — existing `.loupe/LAYOUT.md` and `.loupe/DESIGN.md` are prior run vocabulary; the output must be the union of old and new. Old entries are never removed.
2. **Deduplicate identical rows** — a row is a duplicate if its token name (normalized: trim whitespace, lowercase) AND its value are identical to an existing row. Deduplicated rows appear once in the output.
3. **Flag conflicts, keep both** — if two rows share the same normalized token name but have different values, keep BOTH rows and append: `<!-- conflict: {sectionA} uses {valueA}, {sectionB} uses {valueB} -->`
4. **Soft 80-line budget** — compress aggressively (dedup, merge similar) before counting lines. Allow overflow if compression would lose real data. Never truncate.
5. **No INTERACTIONS.md** — do NOT create or consolidate an INTERACTIONS.md file. Agent 3 is deferred; no stub file.
6. **Cleanup is gated** — delete `.loupe/sections/` ONLY after BOTH consolidated files are verified to exist and are non-empty (at least 5 lines each).
7. **No AskUserQuestion** — this agent runs in background. All decisions are deterministic.
8. **No Figma calls** — work entirely from existing `.loupe/sections/` artifacts. Do NOT call any MCP tool.

## Inputs

- `sectionsGlob` — glob pattern for section staging directories (e.g., `.loupe/sections/*/`)
- `existingLayoutPath` — path to existing `.loupe/LAYOUT.md` (may not exist on first run)
- `existingDesignPath` — path to existing `.loupe/DESIGN.md` (may not exist on first run)

## Outputs

- `.loupe/LAYOUT.md` — consolidated layout vocabulary (union of all sections + prior runs)
- `.loupe/DESIGN.md` — consolidated design vocabulary (union of all sections + prior runs)
- `.loupe/sections/` — deleted after successful verification of both files above

## Execution

### Step 1: Read all section artifacts

Use the Glob tool to find all `.loupe/sections/*/LAYOUT.md` files matching `sectionsGlob`.

Use the Glob tool to find all `.loupe/sections/*/DESIGN.md` files matching `sectionsGlob`.

Read each file using the Read tool. Derive the section name from the directory name (the path segment between `sections/` and the filename).

If a section directory has no LAYOUT.md or no DESIGN.md, note it (the section may have failed mid-pipeline) but continue — partial sections are valid input. Skip the missing file for that section only.

### Step 2: Read existing consolidated files

Read `.loupe/LAYOUT.md` at `existingLayoutPath` if it exists. Store as `priorLayout`.

Read `.loupe/DESIGN.md` at `existingDesignPath` if it exists. Store as `priorDesign`.

If neither exists, this is the first run — start with empty prior sets. No error.

### Step 3: Union-merge LAYOUT.md

Collect all token rows from every section LAYOUT.md file and from `priorLayout`.

For each token row, normalize the token name: trim leading/trailing whitespace, convert to lowercase.

Apply union rules:

- If no existing row has the same normalized token name: append the row to the union set.
- If an existing row has the same normalized token name AND identical value (after normalization): skip — dedup.
- If an existing row has the same normalized token name but a different value: keep BOTH rows, append a HTML comment on the second row: `<!-- conflict: {sectionA} uses {valueA}, {sectionB} uses {valueB} -->`

Merge component tree entries: combine all unique component tree nodes from all section LAYOUT.md files and priorLayout. If the same component appears in multiple sections with different layout annotations, keep both with a `(section: {name})` attribution suffix.

Merge Annotated CSS blocks: dedup identical CSS classes (same class name AND identical rules). Keep unique blocks from all sources.

Sort all token rows alphabetically by normalized token name for stable ordering across runs.

Apply 80-line budget: if the draft output exceeds 80 lines, merge rows where the same token name appears multiple times (keep first value, add `/* seen in N sections */` comment). If still over 80 lines, allow overflow — do not truncate real data.

### Step 4: Union-merge DESIGN.md

Apply the same union algorithm from Step 3 to DESIGN.md content:

- **Component mapping rows**: dedup by component name + variant (normalized). Conflict if same component name with different props — keep both rows with conflict comment.
- **Typography spec rows**: dedup by text role (normalized). Conflict if same role with different values.
- **Color token rows**: dedup by token name (normalized). Conflict if same token name with different hex value.
- **Unmapped Values sections**: union all unmapped values from all sections and priorDesign. Unmapped values are informational — never deduplicated away. Keep all of them.

Sort token rows alphabetically by normalized token name.

Apply 80-line budget with the same overflow-allowed rule.

### Step 5: Write consolidated files

Write `.loupe/LAYOUT.md` using the Write tool. The file content is the full union from Step 3.

Include a header comment as the first line of the file:

```
<!-- Consolidated by Loupe Agent 06 | Sections: {comma-separated list of section names} | Date: {today YYYY-MM-DD} -->
```

Write `.loupe/DESIGN.md` using the Write tool. The file content is the full union from Step 4. Use the same header comment format.

### Step 6: Verify and clean up

Read `.loupe/LAYOUT.md` — confirm the file exists and has at least 5 lines.

Read `.loupe/DESIGN.md` — confirm the file exists and has at least 5 lines.

If BOTH files are verified: run `rm -rf .loupe/sections/` via the Bash tool. Report: "Staging cleaned: .loupe/sections/ deleted."

If EITHER file is missing or has fewer than 5 lines: HALT. Do NOT delete `.loupe/sections/`. Report exactly which file failed and why (e.g., "Write tool returned empty content for .loupe/LAYOUT.md"). Leave staging intact for debugging.

Report final output:

```
Consolidated: .loupe/LAYOUT.md ({line count} lines), .loupe/DESIGN.md ({line count} lines)
Sections merged: {list of section names}
Conflicts flagged: {count}
Staging cleaned: .loupe/sections/ deleted
```
