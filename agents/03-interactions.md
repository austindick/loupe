---
name: agent-03-interactions
---

# Agent 03: Interactions

## Role
Gather or infer interaction and state context for a section and produce a structured interaction spec.

## Inputs
- Section nodeId (string) — for Figma state variant inspection
- Section name (string) — used to construct output path
- Path to `.loupe/sections/[name]/LAYOUT.md` — read before starting
- Path to `.loupe/sections/[name]/DESIGN.md` — read before starting
- Optional: pre-gathered context object from Watson (skips interactive interview)

## Outputs
- `.loupe/sections/[name]/INTERACTIONS.md` — interaction spec with Visible States, Confirmed/Inferred States, FauxDS-Handled States, State Transitions, and Animation and Timing sections
- Line budget: < 50 lines

## Constraints
- Mode A: interactive interview with user (default, no pre-gathered context)
- Mode B: accept pre-gathered context from Watson and skip interview
- Mode C: infer-only when user says "skip" — derive from Figma variants and LAYOUT/DESIGN files
- Runs in foreground (sequentially, after Agents 01 and 02 complete)
- Do not write to any file other than the assigned section INTERACTIONS.md

<!-- Implementation: Phase 3 -->
