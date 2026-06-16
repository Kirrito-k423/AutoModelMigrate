---
phase: aimf-03-correctness-and-accuracy-harness
plan: "01"
subsystem: validation-contracts
tags: [correctness, validation, recipe, minimax-m3, npu-blockers]

requires:
  - phase: aimf-02-core-migration-architecture
    provides: ValidationSuite, Recipe, DataAdapter, BackendAdapter, and lifecycle contracts
provides:
  - Correctness validation harness specification
  - Validation recipe schema
  - MiniMax M3 first-slice validation recipe example
affects: [phase-04-optimization, phase-05-packaging]

tech-stack:
  added: []
  patterns:
    - Ordered correctness gates before optimization
    - Reproducible validation recipes
    - Runtime blockers separated from failed validation

key-files:
  created:
    - docs/framework/correctness-validation-harness.md
    - docs/framework/schemas/validation-recipe.schema.json
    - docs/framework/examples/veomni-minimax-m3.validation-recipe.yaml
  modified: []

key-decisions:
  - "A smoke run is only one correctness gate and cannot unlock optimization alone."
  - "Validation recipes require baseline, candidate, environment, fixtures, gates, results, and evidence."
  - "Ascend NPU validation stays blocked-runtime until root npu-smi, CANN, torch_npu, tensor smoke, and VeOmni smoke evidence exists."

patterns-established:
  - "Gate status supports pending, passed, failed, blocked, and skipped."
  - "MiniMax M3 recipe preserves deferred MSA, multimodal, long-context, and NPU gates while keeping the first slice tiny/text-only."

requirements-completed:
  - FLOW-03
  - VAL-01

duration: 12 min
completed: 2026-06-16
---

# Phase 03 Plan 01: Correctness Validation Harness Summary

**Ordered correctness gates and reproducible validation recipe contract for AI Infra migrations**

## Performance

- **Duration:** 12 min
- **Started:** 2026-06-16T11:02:00Z
- **Completed:** 2026-06-16T11:14:00Z
- **Tasks:** 3
- **Files modified:** 3

## Accomplishments

- Defined the correctness validation harness with manifest, config, tokenizer/processor, checkpoint, construction, forward/logit, loss, determinism, backend runtime, and transition gates.
- Created a Draft 2020-12 JSON Schema for validation recipes.
- Added a MiniMax M3 first-slice validation recipe example that is tiny/text-only while preserving deferred MSA, multimodal, long-context, and Ascend NPU gates.

## Task Commits

1. **Task 1-3: Define correctness harness, schema, and MiniMax M3 recipe** - `f170648` (feat)

## Files Created/Modified

- `docs/framework/correctness-validation-harness.md` - Correctness gate order, semantics, evidence requirements, and MiniMax M3 first-slice guidance.
- `docs/framework/schemas/validation-recipe.schema.json` - Machine-checkable validation recipe schema.
- `docs/framework/examples/veomni-minimax-m3.validation-recipe.yaml` - Example validation recipe for VeOmni + MiniMax M3 first slice.

## Decisions Made

- Correctness gates are ordered and evidence-backed.
- `blocked` is a valid validation status for missing runtime prerequisites and is separate from `failed`.
- NPU support claims remain blocked until the Ascend runtime gates pass.

## Deviations from Plan

The three planned files were created in a single implementation commit because their fields are tightly coupled and were validated together.

**Total deviations:** 1 harmless batching deviation.
**Impact on plan:** No scope change.

## Issues Encountered

None.

## User Setup Required

None.

## Next Phase Readiness

Plan 03-02 can consume the validation recipe contract to define accuracy/drift signoff and lifecycle-blocking behavior.

## Self-Check: PASSED

- `rg "manifest|config|tokenizer|processor|checkpoint|construction|forward|logit|loss|deterministic|backend runtime|transition" docs/framework/correctness-validation-harness.md`
- `python3 -m json.tool docs/framework/schemas/validation-recipe.schema.json >/dev/null`
- `python3 - <<'PY'\nimport yaml\nwith open('docs/framework/examples/veomni-minimax-m3.validation-recipe.yaml', encoding='utf-8') as f:\n    yaml.safe_load(f)\nPY`
- `rg -n "Ascend NPU.*(passed|native|optimized)|NPU execution.*(passed|ready|supported)|MSA.*(native|optimized)" docs/framework/examples/veomni-minimax-m3.validation-recipe.yaml docs/framework/correctness-validation-harness.md` returned no matches.

---
*Phase: aimf-03-correctness-and-accuracy-harness*
*Completed: 2026-06-16*
