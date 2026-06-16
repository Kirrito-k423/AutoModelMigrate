---
phase: 01-veomni-minimax-m3-intake
plan: 01
subsystem: docs
tags: [veomni, minimax-m3, intake, sparse-attention, multimodal, npu]

requires: []
provides:
  - MiniMax M3 fact and assumption log
  - VeOmni integration intake
  - First vertical slice candidate for gap analysis
affects: [phase-01, phase-02, model-spec, framework-adapter, backend-adapter, validation-suite]

tech-stack:
  added: []
  patterns:
    - Source-backed migration intake
    - Fact versus assumption table
    - Validation-first vertical slice selection

key-files:
  created:
    - docs/cases/veomni-minimax-m3/assumptions.md
    - docs/cases/veomni-minimax-m3/intake.md
  modified: []

key-decisions:
  - "Treat MiniMax M3 MSA, 1M context, and multimodality as first-class intake dimensions."
  - "Use a tiny construction or forward smoke as the first VeOmni vertical slice before full checkpoint, distributed, or NPU execution."
  - "Keep Ascend NPU execution gated until driver/DCMI, CANN, torch_npu, tensor smoke, and VeOmni smoke pass."

patterns-established:
  - "Separate public facts from assumptions and validation needs before implementation."
  - "Represent backend differences as capability states instead of model-code branches."

requirements-completed: [M3-01, M3-02]

duration: 5min
completed: 2026-06-16
---

# Phase 1 Plan 01 Summary: VeOmni + MiniMax M3 Intake

**Source-backed MiniMax M3 intake with explicit MSA, long-context, multimodal, VeOmni, and NPU gate boundaries**

## Performance

- **Duration:** 5 min
- **Started:** 2026-06-16T03:29:18Z
- **Completed:** 2026-06-16T03:33:58Z
- **Tasks:** 2
- **Files modified:** 2

## Accomplishments

- Created a MiniMax M3 fact and assumption log covering architecture, MSA/sparse attention, 1M context, multimodality, tokenizer, checkpoint, precision, inference, training, evaluation, and Ascend A2/910B runtime.
- Created a VeOmni integration intake that names extension surfaces for model construction, processor/data path, attention, distributed recipes, checkpoints, and Ascend support.
- Selected the first vertical slice: MiniMax M3 artifact/config intake to VeOmni model/processor gap analysis to text-only tiny construction or forward smoke with explicit MSA, multimodal, and NPU capability statuses.

## Task Commits

Each task was committed atomically:

1. **Task 1: Create MiniMax M3 fact and assumption log** - `0947fee` (docs)
2. **Task 2: Create VeOmni integration intake** - `54483c5` (docs)

**Plan metadata:** pending summary commit

## Files Created/Modified

- `docs/cases/veomni-minimax-m3/assumptions.md` - Fact, assumption, risk, and validation table for MiniMax M3.
- `docs/cases/veomni-minimax-m3/intake.md` - Case intake with scope, source/target, model dimensions, VeOmni extension points, backend scope, gates, and first slice.

## Decisions Made

- The first useful implementation slice should not load or train the full 428B-class model. It should first prove artifact/config intake, processor/tokenizer understanding, and a tiny VeOmni construction or forward path.
- MSA is a backend/model capability that can be unsupported, emulated, native, or optimized. It should not be hidden as a dense-attention fallback.
- The current Ascend NPU path remains gated. Documentation may cite the VeOmni A2/910B Docker guide, but NPU execution is not ready until runtime gates pass.

## Deviations from Plan

None - plan executed exactly as written.

## Issues Encountered

- MiniMax M3 is recent and public details may move. The intake keeps URL-backed claims and marks artifact details such as tokenizer class, shard layout, and precision policy as validation work.
- Current-host NPU evidence is not sufficient for framework smoke. This is captured as a blocker, not treated as a failed MiniMax M3 integration.

## User Setup Required

None for this plan.

## Next Phase Readiness

Plan 01-02 can now use the case intake and assumption log to produce a gap analysis and choose concrete adapter, backend, data, validation, and optimization backlog items.

---
*Phase: 01-veomni-minimax-m3-intake*
*Completed: 2026-06-16*
