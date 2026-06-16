# Phase 4: Backend Optimization Loop - Context

**Gathered:** 2026-06-16
**Status:** Ready for planning

<domain>
## Phase Boundary

Phase 4 defines the backend optimization loop that begins only after semantic correctness gates are green. It specifies how GPU/NPU performance baselines, profiler evidence, backend capability maturity, optimization experiments, regression guards, and rollback criteria are recorded for reusable AI infrastructure migrations.

</domain>

<decisions>
## Implementation Decisions

### Performance Evidence Model
- **D-04-01:** Performance validation must capture throughput, latency, memory, utilization, compile overhead, runtime stability, backend, dtype, hardware, sequence/context length, batch shape, and recipe/config identity.
- **D-04-02:** A performance baseline is a required artifact before optimization starts. Baselines may be `blocked` for a backend when runtime prerequisites are missing, but the blocker must be scoped and evidenced.
- **D-04-03:** Backend profiles should be comparable across GPU and NPU without pretending the runtimes expose identical counters. Use a common top-level schema plus backend-specific metric namespaces.

### Optimization Experiment Loop
- **D-04-04:** Each optimization attempt must record baseline, hypothesis, change, expected bottleneck, before/after metrics, correctness regression check, acceptance result, and rollback criteria.
- **D-04-05:** Optimization cannot claim success by speed alone. Any optimized path must keep correctness/parity gates green for the declared scope before capability maturity can move to `optimized`.
- **D-04-06:** Optimization records should produce backlog updates when a bottleneck remains unresolved, including owner layer, backend scope, severity, evidence, first action, and acceptance gate.

### Backend Capability Integration
- **D-04-07:** GPU/NPU differences stay in `BackendAdapter` capability descriptors and `backend-capability-matrix` rows. Model or framework adapters may consume backend data, but must not grow scattered accelerator branches.
- **D-04-08:** Capability maturity can move to `optimized` only when profiler output, before/after metrics, and regression guards are linked as evidence.
- **D-04-09:** Ascend NPU performance work remains blocked until runtime gates close: root-approved `npu-smi`, CANN activation, matching PyTorch/PTA or `torch_npu`, tensor smoke, and smallest VeOmni NPU smoke.

### MiniMax M3 First Case
- **D-04-10:** The first optimization slice should stay aligned with the Phase 3 tiny text validation path before expanding to MSA, multimodal, long-context, distributed, or NPU performance claims.
- **D-04-11:** MSA, long-context memory, distributed communication, precision policy, and compile/graph behavior must remain explicit optimization dimensions even when the first runnable slice is smaller.

### the agent's Discretion
- The planner may choose exact filenames and schema shape, but should prefer framework docs under `docs/framework/` and examples that extend existing lifecycle, capability, validation, and backlog artifacts.
- The planner may keep runtime-specific profiler fields extensible rather than overfitting to one vendor tool, as long as required common metrics remain clear.

</decisions>

<canonical_refs>
## Canonical References

**Downstream agents MUST read these before planning or implementing.**

### Project Scope
- `.planning/ROADMAP.md` — Phase 4 goal, requirements, success criteria, and plan list.
- `.planning/REQUIREMENTS.md` — `VAL-03`, `VAL-04`, `ACC-01`, `ACC-02`, and `ACC-03` traceability.
- `.planning/PROJECT.md` — core constraints, especially correctness-before-optimization and backend capability modeling.

### Prior Phase Context
- `.planning/phases/aimf-03-correctness-and-accuracy-harness/03-CONTEXT.md` — validation gates and lifecycle transition rules that optimization must preserve.
- `.planning/phases/aimf-02-core-migration-architecture/02-CONTEXT.md` — adapter boundaries, lifecycle ownership, and capability descriptor decisions.

### Framework Docs
- `docs/framework/adapter-contracts.md` — `OptimizationLoop`, `BackendAdapter`, `Recipe`, and `ValidationSuite` responsibilities.
- `docs/framework/backend-capability-matrix.md` — capability maturity states and evidence rules for `optimized`.
- `docs/framework/validation-result-lifecycle.md` — transition blocking semantics and Scale -> Optimize / Optimize -> Accuracy Signoff evidence.
- `docs/framework/correctness-validation-harness.md` — correctness gates that must remain green before optimization claims.
- `docs/framework/backlog-taxonomy.md` — backlog fields for unresolved performance and backend gaps.
- `docs/framework/migration-manifest-spec.md` — manifest surface that future performance profile references should attach to.
- `docs/ops/ascend-npu-runtime.md` — current Ascend NPU runtime blockers and required evidence gates.

</canonical_refs>

<code_context>
## Existing Code Insights

### Reusable Assets
- `docs/framework/backend-capability-matrix.md`: already defines `optimized` maturity and required evidence.
- `docs/framework/validation-result-lifecycle.md`: already defines Optimize-related lifecycle transitions.
- `docs/framework/adapter-contracts.md`: already names `OptimizationLoop` as the owner of profiler-backed tuning.
- `docs/framework/correctness-validation-harness.md`: already defines correctness gates that optimization must not bypass.

### Established Patterns
- Framework artifacts are Markdown specs under `docs/framework/` with clear owner layers, evidence tables, examples, and review checklists.
- Planning artifacts keep MiniMax M3 / VeOmni as the first case while separating reusable contracts from case-specific blockers.
- Backend support claims are scoped; blocked Ascend NPU evidence does not block non-NPU reference work.

### Integration Points
- Add or update framework docs that connect `OptimizationLoop` to `BackendAdapter`, `Recipe`, `ValidationSuite`, lifecycle states, and backlog taxonomy.
- Extend capability matrix guidance with profiler-backed maturity movement and regression evidence.
- Provide MiniMax M3 examples that keep MSA, long-context, precision, distributed, and NPU profiler questions visible without claiming unavailable runtime support.

</code_context>

<specifics>
## Specific Ideas

- Prefer a common performance profile schema with backend-specific metric namespaces.
- Treat compile overhead and runtime stability as first-class metrics, not side notes.
- Keep optimization experiments reproducible through manifest, recipe, environment, hardware, dtype, and fixture references.

</specifics>

<deferred>
## Deferred Ideas

None — discussion stayed within phase scope.

</deferred>

---

*Phase: 4-Backend Optimization Loop*
*Context gathered: 2026-06-16*
