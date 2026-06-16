---
phase: aimf-04-backend-optimization-loop
plan: "04-01"
subsystem: performance-profile-contracts
tags: [performance, profiling, optimization, schema, minimax-m3]

requires:
  - phase: aimf-03-correctness-and-accuracy-harness
    provides: Correctness and accuracy gates from Phase 3
provides:
  - Performance profile specification
  - Performance profile JSON schema
  - MiniMax M3 tiny text performance profile example
  - README discoverability updates for Phase 4 performance evidence
affects: [phase-04-optimization, phase-05-packaging]

tech-stack:
  added: []
  patterns:
    - Backend-neutral top-level performance profile contract
    - Backend-specific metric namespaces for GPU and NPU comparison
    - Profiler-backed optimization gated by correctness and rollback criteria

key-files:
  created:
    - docs/framework/performance-profile-spec.md
    - docs/framework/schemas/performance-profile.schema.json
    - docs/framework/examples/veomni-minimax-m3.performance-profile.yaml
  modified:
    - docs/framework/README.md

key-decisions:
  - "Performance profiles use a common top-level contract with backend-specific metric namespaces so GPU and NPU evidence can be reviewed side by side."
  - "Compile overhead and runtime stability are required evidence, not optional notes."
  - "Optimization may begin only after correctness-green evidence exists for the declared slice."
  - "Ascend NPU blockers remain blocked evidence only; they do not become a support claim."

patterns-established:
  - "The new performance profile spec names workload identity, profiler capture, bottleneck evidence, and regression guard inputs explicitly."
  - "The schema requires baseline, candidate, workload, profiler, bottleneck, regression guard, status, and evidence fields."
  - "The MiniMax M3 example keeps tiny text, MSA, long-context, multimodal, distributed, and Ascend NPU surfaces visible without broadening scope."

requirements-completed:
  - VAL-03
  - VAL-04

duration: 18 min
completed: 2026-06-16
---

# Phase 04 Plan 01: Performance Profile Contract Summary

## Performance

- **Duration:** 18 min
- **Started:** 2026-06-16T00:00:00Z
- **Completed:** 2026-06-16T00:18:00Z
- **Tasks:** 4
- **Files modified:** 5

## Accomplishments

- Created `docs/framework/performance-profile-spec.md` as the human-readable contract for profiler-backed performance evidence.
- Created `docs/framework/schemas/performance-profile.schema.json` as a Draft 2020-12 schema for backend-neutral performance profiles with backend-specific metric namespaces.
- Added `docs/framework/examples/veomni-minimax-m3.performance-profile.yaml` for the Phase 3 tiny text slice, including compile overhead, runtime stability, and blocked Ascend NPU evidence.
- Updated `docs/framework/README.md` so the new performance evidence model is discoverable from the framework index before optimization work begins.

## Task Commits

| Task | Commit | Notes |
|------|--------|-------|
| Tasks 1-4 | docs: add Phase 4 performance profile contract bundle | All four plan tasks were implemented together because the spec, schema, example, and index links share one field vocabulary. |

## Files Created/Modified

- `docs/framework/performance-profile-spec.md` - Human-readable performance profile contract.
- `docs/framework/schemas/performance-profile.schema.json` - Draft 2020-12 JSON Schema for performance profiles.
- `docs/framework/examples/veomni-minimax-m3.performance-profile.yaml` - Tiny text MiniMax M3 performance profile example with blocked Ascend NPU evidence.
- `docs/framework/README.md` - Framework index links to the new performance evidence artifacts.
- `.planning/phases/aimf-04-backend-optimization-loop/04-01-SUMMARY.md` - This summary.

## Deviations From Plan

One harmless batching deviation: the four documentation tasks were completed together instead of one at a time because the schema, example, and index text all depend on the same profile vocabulary and are easier to review as a single contract bundle.

## Verification Evidence

- `rg "throughput|latency|memory|utilization|compile overhead|runtime stability|backend-specific|sequence|context length|batch shape|recipe|config identity" docs/framework/performance-profile-spec.md`
- `python3 -m json.tool docs/framework/schemas/performance-profile.schema.json >/dev/null`
- `python3 - <<'PY'\nimport yaml\nwith open('docs/framework/examples/veomni-minimax-m3.performance-profile.yaml', encoding='utf-8') as f:\n    yaml.safe_load(f)\nPY`
- `rg "performance-profile-spec|performance-profile.schema|veomni-minimax-m3.performance-profile" docs/framework/README.md`

## Self-Check

- [x] Frontmatter present.
- [x] Commit table present.
- [x] Deviations documented.
- [x] Verification evidence captured.
- [x] Ascend NPU remains blocked evidence only.
- [x] Scope stayed inside the declared Phase 4 files.

---
*Phase: aimf-04-backend-optimization-loop*
*Plan: 04-01*
*Completed: 2026-06-16*
