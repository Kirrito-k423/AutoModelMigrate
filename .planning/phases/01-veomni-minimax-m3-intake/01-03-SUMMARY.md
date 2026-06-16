---
phase: 01-veomni-minimax-m3-intake
plan: 03
subsystem: docs
tags: [templates, intake, gap-analysis, reusable-framework, minimax-m3]

requires:
  - phase: 01-veomni-minimax-m3-intake
    provides: MiniMax M3 intake and gap analysis
provides:
  - Reusable migration intake template
  - Reusable gap analysis template
  - MiniMax M3 reusable deltas note
affects: [phase-02, phase-05, migration-workflow, framework-templates]

tech-stack:
  added: []
  patterns:
    - Generic intake template separated from case-specific facts
    - Generic gap template with owner layers and blocking status
    - Reusable deltas note for case-derived architecture inputs

key-files:
  created:
    - docs/framework/migration-intake-template.md
    - docs/framework/gap-analysis-template.md
    - docs/cases/veomni-minimax-m3/reusable-deltas.md
  modified: []

key-decisions:
  - "Promote source/target, backend scope, custom operators, context limits, modality contracts, validation, performance, and signoff fields into the generic intake template."
  - "Promote owner layer, severity, first action, blocking status, selected slice, and non-goals into the generic gap template."
  - "Keep MiniMax Sparse Attention, 1M context, native multimodality, VeOmni A2/910B Docker, and root-only npu-smi behavior as case-specific facts."

patterns-established:
  - "Templates capture recurring migration structure while reusable-deltas documents why each new field exists."
  - "Future migrations should start from facts and assumptions, then select a small vertical slice before coding."

requirements-completed: [M3-04]

duration: 4min
completed: 2026-06-16
---

# Phase 1 Plan 03 Summary: Reusable Template Deltas

**Reusable migration intake and gap-analysis templates extracted from the MiniMax M3 case**

## Performance

- **Duration:** 4 min
- **Started:** 2026-06-16T03:43:52Z
- **Completed:** 2026-06-16T03:47:24Z
- **Tasks:** 2
- **Files modified:** 3

## Accomplishments

- Created a generic migration intake template covering source/target framework, model/feature dimensions, backend scope, dataset/checkpoint scope, validation gates, performance gates, and signoff.
- Created a generic gap-analysis template with ModelSpec, FrameworkAdapter, BackendAdapter, DataAdapter, ValidationSuite, and OptimizationLoop owner layers.
- Created a MiniMax M3 reusable-deltas note that separates promoted reusable fields from case-specific facts.

## Task Commits

Each task was committed atomically:

1. **Task 1: Create reusable intake template** - `caadfbf` (docs)
2. **Task 2: Create reusable gap analysis template and deltas** - `ef27877` (docs)

**Plan metadata:** pending summary commit

## Files Created/Modified

- `docs/framework/migration-intake-template.md` - Generic intake template for future framework/model/backend migrations.
- `docs/framework/gap-analysis-template.md` - Generic gap template with owner layers, severity, first action, and blocking status.
- `docs/cases/veomni-minimax-m3/reusable-deltas.md` - Explanation of template fields promoted from sparse attention, long context, multimodality, and accelerator portability.

## Decisions Made

- The generic intake template should ask about custom operators/kernels, context/shape limits, modality contracts, backend scope, validation, performance, and signoff without making MiniMax-only fields mandatory.
- The generic gap template should require owner layer, severity, first action, and blocking status for every gap.
- MiniMax M3 facts remain case-specific unless they generalize into template prompts.

## Deviations from Plan

None - plan executed exactly as written.

## Issues Encountered

None.

## User Setup Required

None for this plan.

## Next Phase Readiness

Phase 1 now has reusable artifacts for Phase 2 architecture: a case intake, gap map, first slice, NPU evidence, GitHub publication path, and templates that future migrations can start from.

---
*Phase: 01-veomni-minimax-m3-intake*
*Completed: 2026-06-16*
