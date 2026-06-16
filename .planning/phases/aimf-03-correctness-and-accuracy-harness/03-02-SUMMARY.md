---
phase: aimf-03-correctness-and-accuracy-harness
plan: "02"
subsystem: validation-contracts
tags: [accuracy, drift, signoff, lifecycle, minimax-m3]

requires:
  - phase: aimf-03-correctness-and-accuracy-harness
    provides: Correctness harness and validation recipe schema from plan 03-01
provides:
  - Accuracy and drift signoff specification
  - Accuracy signoff schema
  - Validation result lifecycle integration
  - MiniMax M3 pending accuracy signoff example
affects: [phase-04-optimization, phase-05-packaging]

tech-stack:
  added: []
  patterns:
    - Accuracy signoff separated from smoke/parity
    - Versioned thresholds and drift policy
    - Validation results blocking lifecycle transitions

key-files:
  created:
    - docs/framework/accuracy-drift-signoff.md
    - docs/framework/schemas/accuracy-signoff.schema.json
    - docs/framework/validation-result-lifecycle.md
    - docs/framework/examples/veomni-minimax-m3.accuracy-signoff.yaml
  modified:
    - docs/framework/README.md

key-decisions:
  - "Accuracy signoff cannot be inferred from tiny smoke, construction, or parity alone."
  - "Thresholds must carry baseline, eval set, dtype/backend scope, comparator, tolerance, blocking behavior, and reviewer evidence."
  - "Validation results can block lifecycle transitions and must feed backlog acceptance gates."

patterns-established:
  - "Signoff status supports pending, passed, failed, blocked, and accepted_risk."
  - "MiniMax M3 signoff example remains pending/blocked until baseline, eval set, candidate execution, and NPU runtime evidence are available."

requirements-completed:
  - VAL-02
  - VAL-01

duration: 12 min
completed: 2026-06-16
---

# Phase 03 Plan 02: Accuracy And Drift Signoff Summary

**Versioned accuracy/drift signoff and lifecycle-blocking validation results**

## Performance

- **Duration:** 12 min
- **Started:** 2026-06-16T11:14:00Z
- **Completed:** 2026-06-16T11:26:00Z
- **Tasks:** 3
- **Files modified:** 5

## Accomplishments

- Defined accuracy and drift signoff boundaries, baseline identity, eval-set versioning, metric definitions, dtype/backend drift policy, and reviewer signoff.
- Created a Draft 2020-12 JSON Schema for accuracy signoff artifacts.
- Added validation result lifecycle guidance showing how pending, failed, blocked, skipped, and accepted-risk results affect lifecycle transitions and backlog gates.
- Added a MiniMax M3 pending accuracy signoff example and linked all Phase 3 validation docs from the framework README.

## Task Commits

1. **Task 1-3: Define accuracy/drift signoff, lifecycle integration, example, and README links** - `3e55d9f` (feat)

## Files Created/Modified

- `docs/framework/accuracy-drift-signoff.md` - Accuracy/drift signoff rules and review checklist.
- `docs/framework/schemas/accuracy-signoff.schema.json` - Machine-checkable accuracy signoff schema.
- `docs/framework/validation-result-lifecycle.md` - Mapping from validation result states to lifecycle transitions and backlog items.
- `docs/framework/examples/veomni-minimax-m3.accuracy-signoff.yaml` - Pending MiniMax M3 signoff example.
- `docs/framework/README.md` - Adds Phase 3 docs, schemas, and examples.

## Decisions Made

- Accuracy signoff remains pending until baseline, eval set, thresholds, candidate execution, and reviewer evidence exist.
- NPU accuracy signoff remains blocked until Ascend runtime and operator gates pass.
- Blocking validation results must be represented as lifecycle and backlog blockers, not just notes.

## Deviations from Plan

The planned docs were implemented together because schema, lifecycle semantics, example YAML, and README links share field names and were validated as a single contract set.

**Total deviations:** 1 harmless batching deviation.
**Impact on plan:** No scope change.

## Issues Encountered

None.

## User Setup Required

None.

## Next Phase Readiness

Phase 4 can define profiling and optimization workflow knowing correctness, accuracy, drift, and transition-blocking contracts are available.

## Self-Check: PASSED

- `rg "baseline|eval set|metric|threshold|drift|dtype|backend|reviewer|tiny smoke|parity alone" docs/framework/accuracy-drift-signoff.md`
- `python3 -m json.tool docs/framework/schemas/accuracy-signoff.schema.json >/dev/null`
- `python3 - <<'PY'\nimport yaml\nwith open('docs/framework/examples/veomni-minimax-m3.accuracy-signoff.yaml', encoding='utf-8') as f:\n    yaml.safe_load(f)\nPY`
- `rg "blocks_transition|Correctness|Scale|Optimize|Accuracy Signoff|Production Ready|backlog" docs/framework/validation-result-lifecycle.md`
- `rg "accuracy-drift-signoff|validation-result-lifecycle|validation-recipe.schema|accuracy-signoff.schema" docs/framework/README.md`
- `rg -n "decision: approved|state: passed|Ascend NPU.*passed|accuracy.*passed|Production Ready.*allowed" docs/framework/examples/veomni-minimax-m3.accuracy-signoff.yaml docs/framework/accuracy-drift-signoff.md docs/framework/validation-result-lifecycle.md` returned no matches.

---
*Phase: aimf-03-correctness-and-accuracy-harness*
*Completed: 2026-06-16*
