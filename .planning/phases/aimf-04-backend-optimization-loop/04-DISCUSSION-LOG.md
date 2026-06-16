# Phase 4: Backend Optimization Loop - Discussion Log

**Date:** 2026-06-16
**Mode:** auto
**Status:** Context captured

## Auto-Selected Gray Areas

### Performance Evidence Model
- **Question:** What should count as required performance evidence?
- **Selected:** Common profile fields for throughput, latency, memory, utilization, compile overhead, stability, backend, dtype, hardware, recipe/config identity, and workload shape.
- **Reason:** This directly satisfies `VAL-03` while keeping reports comparable across backends.

### Optimization Experiment Loop
- **Question:** How should optimization attempts be recorded?
- **Selected:** Baseline -> hypothesis -> change -> before/after metrics -> correctness regression check -> acceptance result -> rollback criteria.
- **Reason:** This satisfies `VAL-04` and makes tuning reviewable instead of anecdotal.

### Backend Capability Integration
- **Question:** Where should GPU/NPU performance differences live?
- **Selected:** Backend capability descriptors and capability matrix rows, consumed by adapters through defined boundaries.
- **Reason:** This preserves the project constraint against scattered accelerator-specific branches.

### MiniMax M3 First Case
- **Question:** How broad should the first optimization example be?
- **Selected:** Start from the Phase 3 tiny text validation path and keep MSA, multimodal, long-context, distributed, and NPU performance dimensions explicit as future/blocked scopes.
- **Reason:** This keeps optimization downstream of correctness and avoids over-claiming runtime support.

## Decisions Captured

- Performance profiles need common top-level metrics and backend-specific namespaces.
- Optimization reports must link profiler evidence and rollback criteria before a path can be called `optimized`.
- Correctness/parity regression checks are mandatory before optimized capability maturity is claimed.
- Ascend NPU optimization remains blocked until runtime gates close.

## Deferred Ideas

None.
