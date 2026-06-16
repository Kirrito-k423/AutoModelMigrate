---
phase: aimf-02-core-migration-architecture
plan: "01"
subsystem: framework-contracts
tags: [manifest, schema, minimax-m3, veomni, backend-capabilities]

requires:
  - phase: 01-veomni-minimax-m3-intake
    provides: MiniMax M3 intake, assumptions, gap analysis, and reusable deltas
provides:
  - Migration manifest JSON Schema skeleton
  - Human-readable migration manifest specification
  - VeOmni + MiniMax M3 example manifest
affects: [phase-03-validation, phase-04-optimization, phase-05-packaging]

tech-stack:
  added: []
  patterns:
    - Schema-first migration contracts
    - Capability maturity separated from runtime blockers
    - MiniMax M3 examples embedded in generic framework docs

key-files:
  created:
    - docs/framework/schemas/migration-manifest.schema.json
    - docs/framework/migration-manifest-spec.md
    - docs/framework/examples/veomni-minimax-m3.manifest.yaml
  modified: []

key-decisions:
  - "Capability maturity remains unsupported/emulated/native/optimized; runtime blockers are separate evidence records."
  - "MiniMax M3 advertised 1M context is represented separately from staged validation targets."
  - "The manifest carries owner layers and evidence pointers for every major contract section."

patterns-established:
  - "Manifest fields are reviewable in Markdown and machine-checkable in JSON Schema."
  - "Case examples live under docs/framework/examples/ while detailed case evidence remains under docs/cases/."

requirements-completed:
  - ARCH-01
  - ARCH-02
  - FLOW-01
  - ACC-03

duration: 10 min
completed: 2026-06-16
---

# Phase aimf-02 Plan 01: Domain Model And Manifest Schema Summary

**Schema-first migration manifest contract with MiniMax M3 example values and explicit backend evidence fields**

## Performance

- **Duration:** 10 min
- **Started:** 2026-06-16T10:33:00Z
- **Completed:** 2026-06-16T10:43:00Z
- **Tasks:** 3
- **Files modified:** 3

## Accomplishments

- Created a Draft 2020-12 JSON Schema skeleton for migration manifests.
- Wrote the human-readable manifest specification covering owner layers, ModelSpec, FrameworkAdapter, BackendAdapter, DataAdapter, Recipe, ValidationSuite, and OptimizationLoop.
- Added a VeOmni + MiniMax M3 manifest example that keeps MSA, 1M context, multimodality, checkpoint gaps, and Ascend NPU runtime blockers explicit.

## Task Commits

Each task was committed atomically:

1. **Task 1: Create manifest JSON Schema skeleton** - `27bbbbb` (feat)
2. **Task 2: Write manifest specification** - `512720d` (feat)
3. **Task 3: Create MiniMax M3 example manifest** - `a552233` (feat)

## Files Created/Modified

- `docs/framework/schemas/migration-manifest.schema.json` - Draft 2020-12 schema skeleton for top-level migration contracts, owner layers, evidence refs, runtime blockers, lifecycle status, and capability maturity.
- `docs/framework/migration-manifest-spec.md` - Human-readable manifest spec and review checklist.
- `docs/framework/examples/veomni-minimax-m3.manifest.yaml` - Example manifest grounded in the first VeOmni + MiniMax M3 case.

## Decisions Made

- Kept `blocked` out of capability maturity enum and used blocker/evidence fields for runtime readiness.
- Treated advertised model limits and validation targets as separate manifest fields.
- Used public URLs and project-doc references only; no private credentials or host secrets were embedded in the example manifest.

## Deviations from Plan

None - plan executed exactly as written.

**Total deviations:** 0 auto-fixed.
**Impact on plan:** No scope change.

## Issues Encountered

None.

## User Setup Required

None - no external service configuration required.

## Next Phase Readiness

Plan 02 can use the manifest status semantics and MiniMax M3 example while defining adapter boundaries and backend capability matrices.

## Self-Check: PASSED

- `python3 -m json.tool docs/framework/schemas/migration-manifest.schema.json >/dev/null`
- `rg "ModelSpec|FrameworkAdapter|BackendAdapter|DataAdapter|Recipe|ValidationSuite|OptimizationLoop" docs/framework/migration-manifest-spec.md`
- `rg "MiniMaxAI/MiniMax-M3|VeOmni|MSA|1M|Ascend|CANN|torch_npu" docs/framework/examples/veomni-minimax-m3.manifest.yaml`

---
*Phase: aimf-02-core-migration-architecture*
*Completed: 2026-06-16*
