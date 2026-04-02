# Loupe

A Claude Code skill that converts Figma frames into pixel-perfect code through a multi-agent pipeline.

Point Loupe at a Figma frame and it decomposes it into sections, extracts layout and design tokens, generates code using your design system components, reviews its own output for accuracy, and consolidates everything into reusable project-level vocabulary files.

<img width="1348" height="538" alt="image" src="https://github.com/user-attachments/assets/9ac635b3-55e1-44ca-b497-d468fd0a82f0" />

## How it works

```
/loupe [figma-frame-url]
```

Loupe runs a 7-agent pipeline:

| Agent | Role | Mode |
|-------|------|------|
| 00 — Decomposer | Splits a Figma frame into named sections | Foreground |
| 01 — Layout | Extracts spacing, padding, radius, and auto-layout structure | Background (parallel) |
| 02 — Tokens | Maps colors, typography, and components to design system tokens | Background (parallel) |
| 03 — Interactions | Extracts hover/focus/active states *(deferred — not yet implemented)* | — |
| 04 — Builder | Generates code from layout + design specs | Foreground (sequential) |
| 05 — Reviewer | QA pass — diffs generated code against specs and fixes issues in-place | Foreground (sequential) |
| 06 — Consolidator | Merges all section artifacts into project-level `.loupe/` vocabulary files | Foreground |

Agents 01 and 02 run in parallel per section for speed. Agents 04 and 05 run sequentially to avoid file conflicts. If a section fails, the pipeline continues with the remaining sections.

## Project structure

```
SKILL.md                              # Pipeline orchestrator
agents/
  00-decomposer.md                    # Frame → sections
  01-layout.md                        # Figma auto-layout → LAYOUT.md
  02-tokens.md                        # Figma styles → DESIGN.md
  03-interactions.md                  # State extraction (deferred)
  04-builder.md                       # Specs → code
  05-reviewer.md                      # QA review + fix
  06-consolidator.md                  # Section merge → project vocabulary
references/
  library.md                          # Design system reference (components, tokens, typography)
  librarian-output-schema.md          # Schema for library.md generation
utilities/
  librarian.md                        # Sub-skill: /loupe:librarian — generates library.md from source
```

## Pipeline output

Each run produces:

- **Code** — implemented sections in your target directory using your design system components
- **`.loupe/LAYOUT.md`** — consolidated layout vocabulary (spacing tokens, component trees, annotated CSS)
- **`.loupe/DESIGN.md`** — consolidated design vocabulary (component mappings, typography, color tokens, unmapped values)

Vocabulary files extend across runs — a second invocation adds new entries without overwriting existing ones.

## Design system reference

Loupe agents read `references/library.md` to map Figma values to your real components and tokens. Generate it from your codebase:

```
/loupe:librarian generate
```

This scans your component source files, token definitions, typography, and icons, then produces a structured reference file that agents consume at runtime.

## Watson integration

Watson (prototype creation skill) can invoke Loupe via the Skill tool with `watson-mode=true`, which suppresses all confirmation prompts and passes pre-gathered context (Figma URL, target directory) directly into the pipeline.

## Installation

Copy the contents of this repo to `~/.claude/skills/loupe/` and the skill will be available in Claude Code as `/loupe`.

## License

MIT
