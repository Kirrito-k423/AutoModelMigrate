# Optimization Loop

## Purpose

The optimization loop is the profiler-backed tuning contract for a migration
that is already correctness-green for the declared slice. It describes how the
framework records baseline evidence, forms a bottleneck hypothesis, applies one
change at a time, compares before/after results, re-runs scoped correctness
checks, and either accepts the optimization or routes the unresolved bottleneck
into backlog.

Optimization is not a substitute for semantic validation. It is only valid
after the declared correctness path is green and the performance baseline exists
for the same slice.

The loop implements the Phase 4 decisions as follows:

- `D-04-04`: every attempt records baseline, hypothesis, change, expected
  bottleneck, before/after metrics, correctness regression check, acceptance
  result, and rollback criteria.
- `D-04-05`: speed alone never proves success; correctness must stay green.
- `D-04-06`: unresolved bottlenecks become backlog items with owner, scope,
  severity, evidence, first action, and acceptance gate.
- `D-04-09`: Ascend NPU work remains blocked evidence until the ordered runtime
  gates are closed.
- `D-04-10`: the first optimization slice stays aligned with the Phase 3 tiny
  text path.
- `D-04-11`: MSA, long-context, distributed, precision, and compile/graph
  dimensions stay explicit even when the first runnable slice is small.

## Ownership

| Layer | Responsibility |
|-------|----------------|
| `OptimizationLoop` | Owns the baseline, hypothesis, change record, before/after metrics, acceptance decision, rollback criteria, and backlog handoff. |
| `ValidationSuite` | Confirms the optimization did not break the declared correctness scope. |
| `BackendAdapter` | Owns backend capability truth, backend-specific counters, runtime blockers, graph/compile behavior, and profiler hooks. |
| `FrameworkAdapter` | Owns the reproducible recipe and config surfaces that produce the capture. |

## Required Inputs

Every optimization attempt must cite:

- The correctness-green validation result for the same slice.
- The baseline performance profile.
- The candidate run or candidate config.
- The exact manifest, recipe, and config identity.
- The backend, dtype, hardware, and workload shape.
- The profiler artifact or log.
- The relevant backend capability row.

## Required Outputs

Every optimization attempt must record:

- Baseline ID.
- Candidate ID.
- Bottleneck hypothesis.
- Change or config diff.
- Before metrics.
- After metrics.
- Correctness regression check.
- Acceptance decision.
- Rollback criteria.
- Backlog item or backlog update when the bottleneck remains unresolved.

## Workflow

1. Confirm correctness is still green for the declared slice.
2. Capture or reference the baseline performance profile.
3. State the bottleneck hypothesis in one sentence.
4. Apply one optimization change at a time.
5. Capture the candidate run with the same declared workload.
6. Compare before/after throughput, latency, memory, utilization, compile
   overhead, and runtime stability.
7. Re-run the scoped correctness regression check.
8. Decide accept, reject, or defer.
9. Record rollback criteria.
10. Update the backend capability row only when profiler evidence and regression
    guard evidence are linked.
11. If the bottleneck remains, create or update a backlog item with owner layer,
    severity, evidence, first action, backend scope, acceptance gate, and
    lifecycle transition.

## Gate Rules

### Correctness Before Optimization

Optimization cannot begin until the same slice has a green correctness result.
This applies to dense fallback, MSA, long-context, distributed, precision, and
compile/graph work. The optimization loop may compare any of those paths, but it
must not become the place where semantic uncertainty is resolved.

### Runtime Blockers Stay Separate

Runtime blockers do not become maturity states. If a backend is blocked, the
record should say so explicitly and point to the blocker evidence. The blocked
state does not prevent reference-path optimization on another backend.

### One Change Per Attempt

Each attempt should isolate one hypothesis and one change set. If multiple
changes are required, split them into separate attempts so the before/after
evidence stays attributable.

### Regression Check Is Mandatory

Any candidate must re-run the scoped correctness check before acceptance.
Performance gain without correctness is a failed attempt, not an optimized path.

### Rollback Criteria Are Required

Rollback criteria must be specific enough to act on without guessing. Include
the correctness threshold, the performance threshold, and the config or code
change that should be reverted.

## Optimization Dimensions

The first optimization slice for MiniMax M3 stays aligned with the Phase 3 tiny
text path, but the loop must keep the larger dimensions visible:

| Dimension | What Must Stay Visible |
|-----------|------------------------|
| Dense fallback | The reference path used for comparison. |
| MSA | Sparse attention behavior, kernel choice, and parity risk. |
| Long-context | Memory pressure, cache behavior, and context-length scaling. |
| Distributed | Communication cost, topology, and scale behavior. |
| Precision | BF16, FP16, F32, or backend-specific mixed precision policy. |
| Compile / graph | Compile overhead, cache reuse, graph capture, and unsupported-op behavior. |

These dimensions may be staged over time, but they should never disappear from
the optimization record once they are in scope.

## Acceptance Rules

An optimization attempt may be accepted only when all of the following are true:

- The declared correctness regression check passes.
- Before/after metrics are recorded for the declared workload.
- The bottleneck hypothesis is supported or rejected explicitly.
- Rollback criteria are recorded.
- The linked backend capability row can move toward `optimized` without
  contradicting runtime blocker evidence.

If the attempt does not meet the acceptance criteria, it should end in a
documented rejection or backlog update rather than an implied win.

## MiniMax M3 Example Scope

For MiniMax M3, the first optimization loop should use the Phase 3 tiny text
slice and compare the dense reference path against the smallest candidate path
that is correctness-green. MSA, long-context memory, distributed communication,
precision policy, and compile/graph behavior stay visible as explicit follow-up
dimensions. Ascend NPU work remains blocked until the runtime gates in
`docs/ops/ascend-npu-runtime.md` are satisfied.

## Review Checklist

- [ ] Correctness is required before optimization begins.
- [ ] Baseline, candidate, change, and bottleneck are all named.
- [ ] Before/after metrics include throughput, latency, memory, utilization,
  compile overhead, and runtime stability.
- [ ] Regression checks and rollback criteria are recorded.
- [ ] Runtime blockers remain separate from maturity claims.
- [ ] Unresolved bottlenecks are routed to backlog.
- [ ] MiniMax M3 keeps dense, MSA, long-context, distributed, precision, and
  compile/graph dimensions visible.
