# Phase 5: Reference Case Packaging - Discussion Log

> **Audit trail only.** Do not use as input to planning, research, or execution agents.
> Decisions are captured in CONTEXT.md - this log preserves the alternatives considered.

**Date:** 2026-06-16  
**Phase:** 5-Reference Case Packaging  
**Areas discussed:** Template pack boundary, Case-specific versus reusable split, Next-case operating guide, Documentation shape

---

## Template Pack Boundary

| Option | Description | Selected |
|--------|-------------|----------|
| Lifecycle template pack | Package intake, gap, manifest, validation, accuracy, performance, optimization, and handoff templates as one migration lifecycle. | yes |
| Intake/gap only | Treat existing intake and gap templates as enough for Phase 5. | |
| Schema-only | Point future users only to JSON/YAML schemas and examples. | |

**User's choice:** Auto-selected lifecycle template pack.  
**Notes:** This is the only option that satisfies Phase 5 success criteria for intake, gap, validation, and optimization templates.

---

## Case-Specific Versus Reusable Split

| Option | Description | Selected |
|--------|-------------|----------|
| Strict separation | Keep MiniMax M3 facts under case docs/examples and keep reusable contracts model-neutral. | yes |
| Promote full case into defaults | Use MiniMax M3 fields and constraints as the default template shape. | |
| Merge examples into templates | Inline case examples directly into every template. | |

**User's choice:** Auto-selected strict separation.  
**Notes:** This preserves M3-04 while avoiding accidental MiniMax-only requirements in future migrations.

---

## Next-Case Operating Guide

| Option | Description | Selected |
|--------|-------------|----------|
| Operator runbook | Provide ordered steps, evidence gates, first-slice selection rules, and review checklist. | yes |
| Narrative recap | Summarize the MiniMax M3 case as a story for future readers. | |
| Tooling-first guide | Focus on automation commands before documenting decision and evidence requirements. | |

**User's choice:** Auto-selected operator runbook.  
**Notes:** Future teams need a concrete starting path more than another retrospective.

---

## Documentation Shape

| Option | Description | Selected |
|--------|-------------|----------|
| Markdown-first package | Add Markdown templates/indexes and link existing schemas/examples. | yes |
| New schema family only | Create additional machine schemas for every artifact in this phase. | |
| Move all templates | Relocate existing templates into a new directory and update every historical reference. | |

**User's choice:** Auto-selected Markdown-first package.  
**Notes:** The repo is documentation/spec-first. Preserving existing template paths lowers churn while allowing a clearer package index.

---

## the agent's Discretion

- Exact filenames and whether new lifecycle templates live under `docs/framework/` or a `docs/framework/templates/` subdirectory.
- Whether schema-backed artifacts get full standalone templates or short wrappers pointing at canonical specs and schemas.

## Deferred Ideas

None.
