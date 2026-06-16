---
phase: aimf-04-backend-optimization-loop
verified: 2026-06-16T13:24:20Z
status: passed
score: 3/3 success criteria verified
---

# Phase 4 Verification

## Goal-Backward Verification

Goal: define GPU/NPU optimization workflow driven by profiler evidence and backend capability descriptors.

1. Backend profiles capture memory, throughput, latency, utilization, compile overhead, and stability.
   Evidence: `docs/framework/performance-profile-spec.md`, `docs/framework/schemas/performance-profile.schema.json`, and `docs/framework/examples/veomni-minimax-m3.performance-profile.yaml` all require and populate those fields.

2. Optimization plans record baseline, hypothesis, change, result, and rollback criteria.
   Evidence: `docs/framework/optimization-loop.md` and `docs/framework/examples/veomni-minimax-m3.optimization-report.md` both record the baseline/candidate pair, bottleneck hypothesis, change diff, before/after metrics, correctness regression check, acceptance decision, and rollback criteria.

3. GPU and NPU differences are represented as backend capabilities rather than model forks.
   Evidence: `docs/framework/backend-capability-matrix.md`, `docs/framework/migration-manifest-spec.md`, `docs/framework/validation-result-lifecycle.md`, and `docs/framework/migration-lifecycle.md` keep backend behavior in capability rows, runtime blockers, and lifecycle evidence gates.

Result: the phase artifacts satisfy the declared goal without collapsing backend-specific behavior into model logic.

## Observable Truths

- `docs/framework/performance-profile-spec.md` defines a backend-neutral top-level profile with backend-specific namespaces.
- `docs/framework/schemas/performance-profile.schema.json` parses cleanly with JSON Schema Draft 2020-12 tooling.
- `docs/framework/examples/veomni-minimax-m3.performance-profile.yaml` parses cleanly and keeps Ascend NPU as blocked evidence only.
- `docs/framework/optimization-loop.md` requires correctness-before-optimization, one change per attempt, rollback criteria, and backlog handoff.
- `docs/framework/examples/veomni-minimax-m3.optimization-report.md` shows a complete tiny-text optimization attempt rather than a bare perf table.
- `docs/framework/backend-capability-matrix.md` keeps `optimized` gated by profiler-backed evidence and separates runtime blockers from maturity.
- `docs/framework/migration-manifest-spec.md` points optimization to concrete artifacts: performance profile, optimization report, capability rows, and correctness result.
- `docs/framework/validation-result-lifecycle.md` and `docs/framework/migration-lifecycle.md` make `Scale -> Optimize` and `Optimize -> Accuracy Signoff` evidence-gated.
- `docs/framework/backlog-taxonomy.md` routes unresolved bottlenecks into owner-layer backlog items with severity, backend scope, acceptance gate, and lifecycle transition.
- `docs/framework/README.md` makes the new performance and optimization artifacts discoverable from the framework index.
- `docs/ops/ascend-npu-runtime.md` still records Ascend NPU as blocked, with root-approved `npu-smi`, CANN, `torch_npu`, tensor smoke, and VeOmni smoke not yet complete.

## Required Artifacts

| Artifact | Result | Notes |
|----------|--------|-------|
| `docs/framework/performance-profile-spec.md` | Present | Covers identity, workload, shared metrics, backend namespaces, profiler capture, blocking, and review rules. |
| `docs/framework/schemas/performance-profile.schema.json` | Present | Required fields include profile, migration, baseline/candidate, backend, workload, metrics, profiler, bottleneck, regression guard, status, and evidence. |
| `docs/framework/examples/veomni-minimax-m3.performance-profile.yaml` | Present | Tiny-text GPU baseline plus blocked Ascend NPU evidence only. |
| `docs/framework/optimization-loop.md` | Present | Baseline-to-backlog optimization loop with correctness gate and rollback criteria. |
| `docs/framework/examples/veomni-minimax-m3.optimization-report.md` | Present | Lab-notebook-style optimization attempt with profiler evidence and correctness regression check. |
| `docs/framework/backend-capability-matrix.md` | Present | `optimized` requires profiler output, before/after metrics, regression checks, and rollback criteria. |
| `docs/framework/migration-manifest-spec.md` | Present | `optimization` section links to concrete evidence artifacts. |
| `docs/framework/validation-result-lifecycle.md` | Present | `Scale -> Optimize` and `Optimize -> Accuracy Signoff` are explicitly gated. |
| `docs/framework/migration-lifecycle.md` | Present | Lifecycle wording preserves correctness-first ordering. |
| `docs/framework/backlog-taxonomy.md` | Present | Optimization backlog items have owner, severity, backend scope, acceptance gate, and lifecycle transition. |
| `docs/framework/README.md` | Present | Framework index links the new Phase 4 artifacts. |
| `docs/ops/ascend-npu-runtime.md` | Present | NPU remains blocked evidence only; no support claim is made. |

## Requirements Coverage

| ID | Coverage | Verdict |
|----|----------|---------|
| `VAL-03` | Performance evidence includes throughput, latency, memory, utilization, compile overhead, and stability. | Verified |
| `VAL-04` | Optimization work is driven by profiler evidence and records before/after metrics with configuration diffs. | Verified |
| `ACC-01` | GPU/NPU differences are modeled as backend capability descriptors rather than model forks. | Verified |
| `ACC-02` | Backend adapters expose supported precision, communication, memory, graph/compile constraints, and kernel availability. | Verified |
| `ACC-03` | Backend features can be marked unsupported, emulated, native, or optimized. | Verified |

### Decision Context D-04-01 through D-04-11

| Decision | Verification |
|----------|--------------|
| `D-04-01` | Performance profile captures throughput, latency, memory, utilization, compile overhead, runtime stability, backend, dtype, hardware, sequence/context length, batch shape, and recipe/config identity. |
| `D-04-02` | Baseline is required before optimization starts; blocked baselines remain scoped and evidenced. |
| `D-04-03` | Shared top-level schema plus backend-specific metric namespaces is documented and implemented. |
| `D-04-04` | Every optimization attempt records baseline, hypothesis, change, expected bottleneck, before/after metrics, correctness regression check, acceptance result, and rollback criteria. |
| `D-04-05` | Optimization success is not claimed on speed alone; correctness must stay green. |
| `D-04-06` | Unresolved bottlenecks are routed to backlog with owner, scope, severity, evidence, first action, and acceptance gate. |
| `D-04-07` | GPU/NPU differences stay in `BackendAdapter` capability descriptors and capability-matrix rows. |
| `D-04-08` | `optimized` maturity requires profiler output, before/after metrics, and regression guards as evidence. |
| `D-04-09` | Ascend NPU performance work remains blocked until the runtime gates close. |
| `D-04-10` | The first optimization slice stays aligned with the Phase 3 tiny text path. |
| `D-04-11` | MSA, long-context, distributed communication, precision policy, and compile/graph behavior remain explicit optimization dimensions. |

## Automated Checks

- `python3 -m json.tool docs/framework/schemas/performance-profile.schema.json >/dev/null` — passed.
- `python3 - <<'PY' ... yaml.safe_load(...) ... PY` on `docs/framework/examples/veomni-minimax-m3.performance-profile.yaml` — passed.
- `rg` checks for performance metrics, optimization loop terms, capability maturity, manifest/lifecycle/backlog wiring, README discoverability, and NPU blocker language — passed.
- `git diff --check` — passed.

## Human Verification Required

None for this phase. The evidence is documentation- and schema-based, and the only runtime-sensitive area, Ascend NPU, remains intentionally blocked in the source docs rather than claimed as supported.

## Gaps Summary

No gaps found. The phase meets the declared success criteria, and the remaining NPU work is explicitly tracked as blocked evidence instead of a support claim.

## Result

Passed.

