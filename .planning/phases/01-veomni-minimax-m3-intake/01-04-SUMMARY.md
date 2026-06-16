---
phase: 01-veomni-minimax-m3-intake
plan: 04
subsystem: ops
tags: [github, ascend, npu, cann, torch-npu, veomni, docker, a2, 910b]

requires: []
provides:
  - GitHub publication runbook
  - Ascend NPU runtime readiness runbook
  - MiniMax M3 case-specific NPU evidence
affects: [phase-01, phase-02, backend-adapter, capability-matrix, repository-operations]

tech-stack:
  added: []
  patterns:
    - Runtime readiness as explicit gates
    - Publication readiness separated from authenticated push
    - Case evidence linked to BackendAdapter design

key-files:
  created:
    - docs/ops/github-publish.md
    - docs/ops/ascend-npu-runtime.md
    - docs/cases/veomni-minimax-m3/npu-runtime-evidence.md
  modified:
    - README.md

key-decisions:
  - "Keep `origin` targeted at `https://github.com/Kirrito-k423/AutoModelMigrate.git`, with authenticated push as a separate gate."
  - "Use the global `ascend-npu-runtime` skill inspector as the evidence source for NPU readiness."
  - "Use VeOmni's upstream Ascend A2/910B Docker guide as the preferred container baseline before inventing a local recipe."
  - "Do not claim NPU execution while root-level DCMI evidence, CANN, and torch_npu gates are missing."

patterns-established:
  - "Operational readiness docs record current evidence, blockers, commands, and verification gates."
  - "Model case docs translate runtime blockers into BackendAdapter capability states."

requirements-completed: [ACC-04, OPS-01]

duration: 5min
completed: 2026-06-16
---

# Phase 1 Plan 04 Summary: GitHub And Ascend NPU Readiness

**GitHub publication gate and Ascend NPU runtime evidence with VeOmni A2/910B Docker guidance**

## Performance

- **Duration:** 5 min
- **Started:** 2026-06-16T03:33:58Z
- **Completed:** 2026-06-16T03:38:43Z
- **Tasks:** 3
- **Files modified:** 4

## Accomplishments

- Created a GitHub publication runbook for `Kirrito-k423/AutoModelMigrate`, including `origin`, `master`, `gh auth login`, repository creation, and `git push -u origin master` gates.
- Created an Ascend NPU runtime readiness document with current host evidence, root-only `npu-smi` behavior, CANN/PTA/torch_npu install order, and verification gates.
- Captured VeOmni's upstream Ascend A2/910B Docker guide as the preferred container baseline, including the 910B CANN 9.0.0 base image and ARM64 Dockerfile path.
- Added a MiniMax M3 case-specific NPU evidence note that maps current blockers to BackendAdapter and capability-matrix design.

## Task Commits

Each task was committed atomically:

1. **Task 1: Document GitHub publication target** - `7393f0c` (docs)
2. **Task 2: Capture Ascend NPU runtime evidence** - `5914ba7` (docs)
3. **Task 3: Tie NPU readiness to the MiniMax M3 case** - `79cc30e` (docs)

**Plan metadata:** pending summary commit

## Files Created/Modified

- `docs/ops/github-publish.md` - Repository target, auth, branch, repo creation, and push-readiness runbook.
- `docs/ops/ascend-npu-runtime.md` - Host evidence and ordered NPU verification gates for CANN/PTA/torch_npu.
- `docs/cases/veomni-minimax-m3/npu-runtime-evidence.md` - Case-specific NPU blockers and BackendAdapter implications.
- `README.md` - Pointer to the GitHub publication runbook.

## Decisions Made

- Publishing is locally prepared but not complete until GitHub authentication and `git push` succeed.
- CANN downloads must come from the official HiAscend community page after version and traffic budget are confirmed.
- VeOmni's upstream A2/910B Docker guide is the first reference for container setup, especially on this `aarch64` host.
- NPU readiness must be proven layer by layer: driver/DCMI, CANN, `torch_npu`, tensor smoke, VeOmni smoke, then MiniMax M3 smoke.

## Deviations from Plan

None - plan executed exactly as written.

## Issues Encountered

- `gh auth status` reports no authenticated GitHub hosts. This blocks pushing but does not block local documentation or planning.
- `npu-smi info` fails as the normal user with DCMI `ret=-8005`; non-interactive sudo requires a password. The user reported root can see `npu-smi info`, so root-level evidence remains a required setup step.
- CANN toolkit and `torch_npu` are absent, so NPU framework smoke is not attempted.

## User Setup Required

- Run `gh auth login` or provide equivalent GitHub credentials before pushing to `Kirrito-k423/AutoModelMigrate`.
- Provide an approved root/privileged path to capture `npu-smi info`.
- Confirm CANN/PTA version matrix and traffic/disk budget before downloading CANN packages or pulling the VeOmni A2/910B image.

## Next Phase Readiness

Plan 01-02 can use these ops artifacts to include GitHub and Ascend NPU readiness as explicit gaps in the first MiniMax M3 vertical slice. NPU work remains a gated backend path, not a hidden prerequisite for model intake.

---
*Phase: 01-veomni-minimax-m3-intake*
*Completed: 2026-06-16*
