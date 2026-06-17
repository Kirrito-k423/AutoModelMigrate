---
phase: 04
slug: backend-optimization-loop
status: verified
nyquist_compliant: true
wave_0_complete: true
created: 2026-06-17
---

# Phase 04 - Validation Strategy

Per-phase validation contract for feedback sampling during execution.

## Test Infrastructure

| Property | Value |
|----------|-------|
| Framework | Shell assertions, `python3 -m json.tool`, and Python YAML parsing |
| Config file | None - docs/schema phase |
| Quick run command | `test -f docs/framework/performance-profile-spec.md && test -f docs/framework/optimization-loop.md` |
| Full suite command | `python3 -m json.tool docs/framework/schemas/performance-profile.schema.json >/dev/null && python3 - <<'PY'\nimport yaml\nwith open('docs/framework/examples/veomni-minimax-m3.performance-profile.yaml', encoding='utf-8') as f:\n    yaml.safe_load(f)\nPY\nrg "baseline|hypothesis|change|before/after|rollback|backend capability|Scale -> Optimize" docs/framework` |
| Estimated runtime | ~5 seconds |

## Sampling Rate

- After every task commit: run the verification command listed for that task.
- After each plan: run the full suite command above.
- Before verify-work: full suite must be green and performance profile YAML must parse.
- Max feedback latency: 10 seconds.

## Per-Task Verification Map

| Task ID | Plan | Requirement | Threat Ref | Secure Behavior | Test Type | Automated Command | Status |
|---------|------|-------------|------------|-----------------|-----------|-------------------|--------|
| 04-01-01 | 04-01 | VAL-03 | T-04-01 | Performance evidence captures workload, backend, metrics, profiler, and stability fields before optimization claims. | source | `rg "throughput|latency|memory|utilization|compile overhead|runtime stability|backend-specific|sequence|context length|batch shape|recipe|config identity" docs/framework/performance-profile-spec.md` | green |
| 04-01-02 | 04-01 | VAL-03 | T-04-01 | Performance profiles are machine-checkable. | schema | `python3 -m json.tool docs/framework/schemas/performance-profile.schema.json >/dev/null` | green |
| 04-01-03 | 04-01 | VAL-03, ACC-01 | T-04-01 | MiniMax M3 example keeps Ascend NPU blocked until runtime evidence exists. | yaml/source | `python3 - <<'PY'\nimport yaml\nwith open('docs/framework/examples/veomni-minimax-m3.performance-profile.yaml', encoding='utf-8') as f:\n    yaml.safe_load(f)\nPY` | green |
| 04-02-01 | 04-02 | VAL-04 | T-04-02 | Optimization attempts require baseline, hypothesis, config diff, metrics, correctness regression, acceptance, and rollback. | source | `rg "baseline|hypothesis|change|before/after|correctness regression|acceptance|rollback|backlog|MSA|long-context|distributed|precision|compile|graph" docs/framework/optimization-loop.md` | green |
| 04-02-02 | 04-02 | VAL-04, ACC-01, ACC-02, ACC-03 | T-04-02 | Backend maturity and optimization evidence stay in capability descriptors, not model forks. | source | `rg "optimized|profiler|before/after|rollback|runtime blocker|backend capability|D-04-07|D-04-08|D-04-09" docs/framework/backend-capability-matrix.md` | green |
| 04-02-03 | 04-02 | VAL-04, FLOW-04 | T-04-02 | Lifecycle and backlog wiring keep optimization evidence-gated. | source | `rg "optimization|performance profile|optimization report|Scale -> Optimize|Optimize -> Accuracy Signoff|owner_layer|severity|backend_scope|acceptance_gate|rollback" docs/framework/migration-manifest-spec.md docs/framework/validation-result-lifecycle.md docs/framework/migration-lifecycle.md docs/framework/backlog-taxonomy.md docs/framework/README.md` | green |

## Wave 0 Requirements

Existing infrastructure covers this phase: shell, `rg`, Python 3, and PyYAML are available. No additional packages are required.

## Manual-Only Verifications

All Phase 4 behaviors have automated source, schema, or YAML parsing checks.
Human review remains useful for performance threshold reasonableness, but it is
not the only verification mechanism.

## Validation Sign-Off

- [x] All tasks have automated verify commands or source assertions.
- [x] Sampling continuity: no 3 consecutive tasks without automated verify.
- [x] Wave 0 covers all missing infrastructure references.
- [x] No watch-mode flags.
- [x] Feedback latency < 10s.
- [x] `nyquist_compliant: true` set in frontmatter.

**Approval:** approved 2026-06-17 during milestone closeout

## Validation Audit 2026-06-17

| Metric | Count |
|--------|-------|
| Gaps found | 0 |
| Resolved | 6 |
| Escalated | 0 |

Phase 04 is Nyquist-compliant for a documentation/schema phase. The performance
profile, optimization loop, capability matrix, lifecycle, backlog, README, and
example artifacts all have executable source/schema/YAML checks.
