---
phase: aimf-05-reference-case-packaging
plan: "05-01"
subsystem: reusable-template-pack
tags: [templates, migration-framework, validation, performance, handoff]
completed: 2026-06-16
status: completed
requirements-completed:
  - M3-04
---

# Phase 05 Plan 01: Reusable Template Package Summary

## Commits

| Task | Commit | Notes |
|------|--------|-------|
| Template pack catalog | `bff6e9e` | Added the lifecycle catalog and preserved existing intake/gap entry points. |
| Manifest and backend templates | `4206fa2` | Added generic manifest YAML and backend capability matrix templates. |
| Validation, accuracy, performance templates | `deee6bc` | Added YAML skeletons for correctness, signoff, and performance evidence. |
| Optimization and handoff templates | `c41371c` | Added lab-notebook and final readiness review templates. |
| README navigation | `bb7d670` | Made `docs/framework/README.md` the template package navigation hub. |

## Files Created Or Modified

- `docs/framework/template-pack.md` - Lifecycle catalog for the template pack.
- `docs/framework/migration-manifest-template.yaml` - Generic manifest skeleton linked to the manifest spec and schema.
- `docs/framework/backend-capability-matrix-template.md` - Generic capability matrix template with runtime blocker guidance.
- `docs/framework/validation-recipe-template.yaml` - Generic validation recipe with separate `failed_correctness` and `blocked_runtime` gates.
- `docs/framework/accuracy-signoff-template.yaml` - Generic accuracy and drift signoff skeleton.
- `docs/framework/performance-profile-template.yaml` - Generic performance profile skeleton with profiler and regression guard fields.
- `docs/framework/optimization-report-template.md` - Profiler-backed before/after optimization report template.
- `docs/framework/migration-handoff-template.md` - Final readiness handoff template.
- `docs/framework/README.md` - Template package navigation and start sequence.
- `.planning/phases/aimf-05-reference-case-packaging/05-01-SUMMARY.md` - This summary.

## Deviations

No scope deviations. The only sequencing choice was to implement the template
files in one edit and commit them in plan-task groups so the shared vocabulary
could be checked consistently.

## Command Evidence

- `rg "Migration Intake|Gap Analysis|Migration Manifest|Backend Capability Matrix|Validation Recipe|Accuracy|Performance Profile|Optimization Report|Migration Handoff|migration-intake-template|gap-analysis-template" docs/framework/template-pack.md` - passed.
- `python3 - <<'PY'\nimport yaml\nfor path in ['docs/framework/migration-manifest-template.yaml','docs/framework/validation-recipe-template.yaml','docs/framework/accuracy-signoff-template.yaml','docs/framework/performance-profile-template.yaml']:\n    with open(path, encoding='utf-8') as f:\n        yaml.safe_load(f)\nprint('yaml ok')\nPY` - passed.
- `rg "unsupported|blocked|emulated|native|optimized|precision|memory|communication|compile|profiler" docs/framework/backend-capability-matrix-template.md` - passed.
- `rg "blocked_runtime|failed_correctness|baseline|threshold|drift|throughput|latency|compile_overhead|runtime_stability|regression_guard" docs/framework/validation-recipe-template.yaml docs/framework/accuracy-signoff-template.yaml docs/framework/performance-profile-template.yaml` - passed.
- `rg "baseline|hypothesis|config diff|before/after|correctness regression|acceptance|rollback|backlog handoff" docs/framework/optimization-report-template.md && rg "evidence inventory|lifecycle state|validation status|accuracy status|performance status|backend capability|unresolved blockers|reusable deltas|reviewer signoff" docs/framework/migration-handoff-template.md` - passed.
- `rg "template-pack|migration-manifest-template|backend-capability-matrix-template|validation-recipe-template|accuracy-signoff-template|performance-profile-template|optimization-report-template|migration-handoff-template|How To Start A New Migration" docs/framework/README.md` - passed.
- `! rg "MiniMax M3|VeOmni|MSA|1M|Ascend A2|910B" docs/framework/*template* docs/framework/template-pack.md || rg "example|case-specific|do not copy" docs/framework/*template* docs/framework/template-pack.md` - passed.

## Self-Check

- [x] Frontmatter present.
- [x] Commit table present.
- [x] Deviations documented.
- [x] Verification evidence listed.
- [x] Existing intake and gap templates remain in place.
- [x] Generic templates avoid model-specific defaults.
- [x] README exposes the full template package.
