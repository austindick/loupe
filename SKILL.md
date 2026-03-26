---
name: loupe
description: Pixel-perfect UI implementation from Figma frames. Use when the user says "/loupe", provides a Figma frame URL, or asks to implement a design from Figma. Decomposes frames into sections and runs a multi-agent pipeline: layout extraction, token/component mapping, code generation, QA review, and project-level consolidation.
---

# Loupe

Loupe is a multi-agent pipeline that converts Figma frames into pixel-perfect code. Agent 0 decomposes the frame into named sections. Agents 1 and 2 extract layout and design tokens in parallel per section. Agent 4 builds each section sequentially; Agent 5 reviews and fixes in-place. Agent 6 consolidates all section artifacts into project-level vocabulary files.

**Design system reference:** @references/library.md

**Artifact schemas:** Agents must conform to canonical examples in `.planning/artifact-schemas/` — LAYOUT-EXAMPLE.md and DESIGN-EXAMPLE.md define the exact section headers and formats required.

## Inputs

- `figmaUrl` — required; Figma frame URL including `/design/` path segment and `node-id=` query parameter
- `targetDir` — optional; directory where code will be written (ask if absent and not watson-mode)
- `watson-mode` — optional flag; when true, suppresses all confirmation prompts and auto-accepts heuristic section lists

## Step 0: Parse Invocation

If invoked via `/loupe` with no URL: prompt "Paste a Figma frame URL to continue." (friendly, not an error).

If a URL is present: validate it contains a `/design/` path segment AND a `node-id=` query parameter. If either is missing, ask: "Please paste the full Figma frame URL — it should look like `https://www.figma.com/design/{fileKey}/...?node-id={id}`."

If the URL is valid and not watson-mode: confirm "Run Loupe on this frame? (Yes/No)". If the user says No, stop.

If watson-mode: skip confirmation.

If `targetDir` is not provided and not watson-mode: ask "Where should I write the code? Provide a directory path."

If watson-mode and `targetDir` is not provided: halt with error — "watson-mode requires a targetDir. Provide a target directory path."

If a Figma frame URL appears in conversation without `/loupe`: offer the same confirmation prompt — "I see a Figma frame URL. Run Loupe on this frame? (Yes/No)". Process only the first URL; mention any others and offer to run them after.

## Step 1: Decompose

Dispatch @agents/00-decomposer.md in **foreground** (never background) with:
- `figmaUrl`: the validated Figma frame URL
- `watson-mode`: pass through as-is

Wait for result: `sections[] = [{name, nodeId, dimensions}]`

If sections is empty, halt: "No sections found. Check that the Figma frame URL points to a frame with child layers, or add `::section` tags to the layers."

Note: Agent 0 is foreground-only because it may use AskUserQuestion for heuristic approval. watson-mode suppresses that prompt inside Agent 0 — it does NOT move Agent 0 to background.

## Step 2: Research (parallel per section)

For each section in sections[]:

Print: `Section {i}/{n}: {section.name} — researching...`

Dispatch @agents/01-layout.md [**background**] with:
- `nodeId`: {section.nodeId}
- `sectionName`: {section.name}
- `libraryPath`: ~/.claude/skills/loupe/references/library.md
- `existingLayoutPath`: .loupe/LAYOUT.md

Dispatch @agents/02-tokens.md [**background**] with:
- `nodeId`: {section.nodeId}
- `sectionName`: {section.name}
- `libraryPath`: ~/.claude/skills/loupe/references/library.md
- `existingDesignPath`: .loupe/DESIGN.md

Wait for both agents to complete before proceeding to Step 3 for this section.

Note: Both background dispatches run simultaneously per section (parallelism is runtime-dependent — the pipeline is correct either way since correctness does not depend on true simultaneous execution).

If either agent fails: mark the section as FAILED, record the error summary, and continue to the next section. Do NOT halt the pipeline.

## Step 3: Build + Review (sequential per section)

For each section in sections[] that is not already marked FAILED from Step 2:

Print: `Section {i}/{n}: {section.name} — building...`

If `targetDir` was not asked yet and is still unknown: ask "What file should I write the code to? Provide the path relative to `{targetDir}` or an absolute path." Use the same target file for all sections unless the user specifies otherwise.

Dispatch @agents/04-builder.md [**foreground, sequential**] with:
- `layoutPath`: .loupe/sections/{section.name}/LAYOUT.md
- `designPath`: .loupe/sections/{section.name}/DESIGN.md
- `targetFilePath`: {targetDir}/{resolvedFile}
- `sectionScope`: {section.name}
- `libraryPath`: ~/.claude/skills/loupe/references/library.md

Wait for Agent 4 to complete, then dispatch @agents/05-reviewer.md [**foreground, sequential**] with:
- `layoutPath`: .loupe/sections/{section.name}/LAYOUT.md
- `designPath`: .loupe/sections/{section.name}/DESIGN.md
- `sourceFilePath`: {targetDir}/{resolvedFile}
- `sectionScope`: {section.name}

Wait for Agent 5 to complete. Record section result: pass/fail, files modified, unmapped count (from reviewer conversation output).

If either agent fails: mark the section as FAILED, record error summary, continue to next section.

## Step 4: Consolidate

After the section loop exits (including any failed sections):

Dispatch @agents/06-consolidator.md with:
- `sectionsGlob`: .loupe/sections/*/
- `existingLayoutPath`: .loupe/LAYOUT.md
- `existingDesignPath`: .loupe/DESIGN.md

Wait for completion.

## Step 5: Summary

Print final summary table:

```
## Pipeline Complete

| Section | Status | Files Modified | Mapped | Unmapped | Issues |
|---------|--------|---------------|--------|----------|--------|
| {name}  | Pass/FAILED | {file} | {n} | {n} | {reason or —} |
```

Report:
- Consolidated files: `.loupe/LAYOUT.md`, `.loupe/DESIGN.md`
- Staging cleanup: `.loupe/sections/ deleted`

If any sections failed: list each with its error reason and note: "You can retry failed sections by running `/loupe` with the individual section frame URL."
