---
phase: aimf-04-backend-optimization-loop
checked: 2026-06-16T13:24:20Z
status: secured
threats_total: 6
threats_open: 0
---

# Phase 4 Security Audit

## Threat Register

1. `T-04-01-01` Schema overfits one profiler/backend and cannot compare GPU and NPU evidence side by side.
2. `T-04-01-02` Compile overhead or runtime stability is omitted because only throughput is visible.
3. `T-04-01-03` The MiniMax M3 example implies NPU execution support before runtime gates exist.
4. `T-04-02-01` Optimization success is claimed from speed alone while correctness regresses or was never re-run.
5. `T-04-02-02` Backend capability rows move to `optimized` without profiler output, before/after metrics, and rollback criteria.
6. `T-04-02-03` The workflow converts a runtime blocker into a semantic failure or hides unresolved bottlenecks instead of routing them to backlog.

## Mitigation Evidence

- `T-04-01-01` is closed by `docs/framework/performance-profile-spec.md`, which defines a backend-neutral top-level profile with shared metrics and backend-specific namespaces, and by `docs/framework/schemas/performance-profile.schema.json`, which requires the shared identity, workload, metrics, profiler, and evidence fields.
- `T-04-01-02` is closed by `docs/framework/performance-profile-spec.md` and `docs/framework/examples/veomni-minimax-m3.performance-profile.yaml`, both of which require and populate compile overhead and runtime stability alongside throughput, latency, memory, and utilization.
- `T-04-01-03` is closed by `docs/framework/examples/veomni-minimax-m3.performance-profile.yaml` and `docs/ops/ascend-npu-runtime.md`, which keep Ascend NPU in blocked evidence state and explicitly reference the missing root-approved `npu-smi`, CANN, `torch_npu`, tensor smoke, and VeOmni smoke gates.
- `T-04-02-01` is closed by `docs/framework/optimization-loop.md`, `docs/framework/examples/veomni-minimax-m3.optimization-report.md`, and `docs/framework/validation-result-lifecycle.md`, which require correctness to stay green, mandate a scoped regression check, and prevent speed-only acceptance.
- `T-04-02-02` is closed by `docs/framework/backend-capability-matrix.md`, which defines `optimized` as profiler-backed tuning with before/after metrics, regression checks, and rollback criteria, and by `docs/framework/migration-manifest-spec.md`, which links optimization to concrete evidence artifacts.
- `T-04-02-03` is closed by `docs/framework/optimization-loop.md`, `docs/framework/backlog-taxonomy.md`, and `docs/framework/migration-lifecycle.md`, which keep runtime blockers separate from maturity and require unresolved bottlenecks to become owner-layer backlog items with evidence, first action, backend scope, acceptance gate, and lifecycle transition.

## Accepted Risks

None. The phase artifacts preserve blocked Ascend NPU readiness as blocked evidence only, and no threat requires an exception to the declared contract.

## Audit Trail

- Reviewed the phase threat registers in `.planning/phases/aimf-04-backend-optimization-loop/04-01-PLAN.md` and `04-02-PLAN.md`.
- Reviewed the phase verification artifact in `.planning/phases/aimf-04-backend-optimization-loop/04-VERIFICATION.md`.
- Reviewed the authoritative contracts in:
  - `docs/framework/performance-profile-spec.md`
  - `docs/framework/schemas/performance-profile.schema.json`
  - `docs/framework/examples/veomni-minimax-m3.performance-profile.yaml`
  - `docs/framework/optimization-loop.md`
  - `docs/framework/examples/veomni-minimax-m3.optimization-report.md`
  - `docs/framework/backend-capability-matrix.md`
  - `docs/framework/migration-manifest-spec.md`
  - `docs/framework/validation-result-lifecycle.md`
  - `docs/framework/migration-lifecycle.md`
  - `docs/framework/backlog-taxonomy.md`
  - `docs/framework/README.md`
  - `docs/ops/ascend-npu-runtime.md`
- Verified that the phase verification file already records the relevant decision coverage and that no residual gap is called out there.

## Result

All plan-time threats for Phase 4 are closed by evidence in the shipped artifacts. The security posture is secure for the declared scope, with Ascend NPU still intentionally blocked rather than claimed as supported.
