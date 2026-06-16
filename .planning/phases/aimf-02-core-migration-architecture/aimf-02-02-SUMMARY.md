---
phase: aimf-02-core-migration-architecture
plan: "02"
subsystem: framework-contracts
tags: [adapters, backend, npu, gpu, capability-matrix]

requires:
  - phase: 01-veomni-minimax-m3-intake
    provides: NPU runtime evidence and MiniMax M3 gap analysis
provides:
  - Adapter contract boundaries
  - Backend capability matrix
  - VeOmni + MiniMax M3 capability example
affects: [phase-03-validation, phase-04-optimization, phase-05-packaging]

tech-stack:
  added: []
  patterns:
    - Owner-layer adapter boundaries
    - BackendAdapter capability descriptors
    - Runtime blocker evidence separated from support maturity

key-files:
  created:
    - docs/framework/adapter-contracts.md
    - docs/framework/backend-capability-matrix.md
    - docs/framework/examples/veomni-minimax-m3-capabilities.md
  modified: []

key-decisions:
  - "Model, framework, backend, data, recipe, validation, and optimization responsibilities are separated into stable contracts."
  - "GPU/NPU differences are represented through BackendAdapter capability descriptors."
  - "Ascend NPU blockers remain evidence records until root npu-smi, CANN, torch_npu, tensor smoke, and VeOmni smoke pass."

patterns-established:
  - "Every adapter contract includes responsibility, inputs, outputs, prohibited ownership, case example, and evidence."
  - "Capability examples distinguish support claims from blocked runtime setup."

requirements-completed:
  - ARCH-01
  - ARCH-03
  - ARCH-04
  - ACC-01
  - ACC-02
  - ACC-03

duration: 10 min
completed: 2026-06-16
---

# Phase aimf-02 Plan 02: Adapter Boundaries And Capability Descriptors Summary

**Owner-layer adapter contracts and backend capability matrix for GPU/NPU migration support**

## Performance

- **Duration:** 10 min
- **Started:** 2026-06-16T10:43:00Z
- **Completed:** 2026-06-16T10:53:00Z
- **Tasks:** 3
- **Files modified:** 3

## Accomplishments

- Defined stable responsibilities for `ModelSpec`, `FrameworkAdapter`, `BackendAdapter`, `DataAdapter`, `Recipe`, `ValidationSuite`, and `OptimizationLoop`.
- Created the backend capability matrix with `unsupported`, `emulated`, `native`, and `optimized` states plus separate runtime blocker fields.
- Added a VeOmni + MiniMax M3 capability example that records GPU reference assumptions, MSA gaps, long-context staging, and Ascend NPU blockers.

## Task Commits

Each task was committed atomically:

1. **Task 1: Write adapter contract spec** - `994447b` (feat)
2. **Task 2: Define backend capability matrix** - `c8a3f46` (feat)
3. **Task 3: Create MiniMax M3 capability example** - `8ced3fa` (feat)

## Files Created/Modified

- `docs/framework/adapter-contracts.md` - Adapter contract boundaries and ownership rules.
- `docs/framework/backend-capability-matrix.md` - Backend capability dimensions, maturity states, and runtime blocker model.
- `docs/framework/examples/veomni-minimax-m3-capabilities.md` - Case-specific capability rows for GPU and Ascend NPU paths.

## Decisions Made

- Adapter docs explicitly prohibit accelerator-specific model semantics and direct support claims without evidence.
- Backend matrix includes ACC-02 dimensions: precision, communication, memory, graph/compile, custom kernels, and profiler hooks.
- MiniMax M3 MSA and Ascend NPU support remain unsupported until evidence closes the listed blockers.

## Deviations from Plan

None - plan executed exactly as written.

**Total deviations:** 0 auto-fixed.
**Impact on plan:** No scope change.

## Issues Encountered

None.

## User Setup Required

None - no external service configuration required.

## Next Phase Readiness

Plan 03 can use the adapter and capability contracts to define lifecycle transitions and backlog taxonomy fields.

## Self-Check: PASSED

- `rg "ModelSpec|FrameworkAdapter|BackendAdapter|DataAdapter|Recipe|ValidationSuite|OptimizationLoop" docs/framework/adapter-contracts.md`
- `rg "unsupported|emulated|native|optimized|runtime blocker" docs/framework/backend-capability-matrix.md`
- `rg "MiniMax M3|MSA|Ascend|CANN|torch_npu|VeOmni" docs/framework/examples/veomni-minimax-m3-capabilities.md`

---
*Phase: aimf-02-core-migration-architecture*
*Completed: 2026-06-16*
