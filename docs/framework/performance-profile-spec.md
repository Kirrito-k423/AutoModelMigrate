# Performance Profile Spec

## Purpose

Performance profiles capture profiler-backed evidence for optimization work
after correctness and accuracy gates are already green. They make performance
results comparable across GPU and NPU backends without moving backend-specific
behavior into model semantics or framework glue.

## Owner Layers

| Layer | Responsibility |
|-------|----------------|
| `OptimizationLoop` | Owns performance baselines, experiments, before/after evidence, regression guards, and rollback criteria. |
| `BackendAdapter` | Owns backend capability truth, backend-specific counters, runtime constraints, compile behavior, and profiler hooks. |
| `FrameworkAdapter` | Owns recipe and configuration surfaces that feed reproducible runs. |
| `ValidationSuite` | Confirms correctness stays green for the declared optimization scope. |

## Profile Identity

Every performance profile must be traceable back to the migration and the exact
artifacts used to produce the evidence.

Required identity fields:

- `profile_id`
- `version`
- `migration_id`
- `manifest_ref`
- `baseline_id`
- `candidate_id`
- `framework_revision`
- `hardware`
- `dtype`

The identity must preserve the exact manifest, recipe, and config references so
performance evidence stays reproducible.

## Baseline And Candidate Identity

The baseline is the known comparison point. The candidate is the optimized or
experimental path being measured.

Record both:

- Source and target names.
- Revision, commit, or artifact pin where available.
- Exact recipe/config identity.
- Whether the comparison is reference, native, or optimized.

Optimization work starts only when the declared correctness scope is already
green and the baseline performance profile exists.

## Workload Description

The workload section must describe both the declared slice and the actual
captured run so reviewers can detect drift.

Required workload fields:

- Backend
- Dtype
- Hardware
- Declared batch shape
- Actual batch shape
- Declared sequence length or context length
- Actual sequence length or context length
- Modality or modal mix
- Scale target or slice name
- Environment activation details

Batch shape, sequence length, and context length must be present because
performance claims are brittle when the captured shape differs from the claimed
shape.

## Metric Groups

The top-level metric model must stay backend-neutral. Every profile must record
these shared metrics:

- Throughput
- Latency
- Memory
- Utilization
- Compile overhead
- Runtime stability

Treat compile overhead and runtime stability as required evidence, not side
notes.

Recommended shared metric fields:

- Observed value or range.
- Unit.
- Measurement window.
- Rerun count or sample count.
- Notes on variance or stability.

## Backend-Specific Namespaces

Backend-specific counters live in a dedicated namespace so GPU and NPU reports
can be compared side by side without flattening vendor differences into the
shared contract.

Rules:

- Keep shared metrics at the top level.
- Put vendor counters and tool-specific names under `backend_specific`.
- Use backend keys such as `gpu` and `ascend_npu`.
- Record backend-specific evidence even when the backend is blocked.

Examples of backend-specific evidence:

- GPU tensor-core or SM counters.
- Ascend NPU compile/runtime counters.
- Memory bandwidth or kernel occupancy data from a vendor profiler.

## Profiler Capture Details

Each profile must show how evidence was collected.

Required profiler details:

- Profiler tool name.
- Capture command.
- Capture window.
- Artifact or log references.
- Framework revision used for the capture.

If the runtime is blocked, record the blocked state explicitly instead of
implying the capture succeeded.

## Blocking And Acceptance Rules

Performance evidence is not a support claim by itself. A profile can be:

- `pending` when capture has not run.
- `passed` when the declared scope met the thresholds.
- `failed` when the measurement or regression check violated thresholds.
- `blocked` when a prerequisite runtime or environment gate prevented capture.

Before optimization may start, the following must exist for the declared slice:

- A correctness-green validation result for the same scope.
- A baseline performance profile.
- A reproducible recipe/config identity.
- Declared workload and actual workload fields.
- Profiler capture details.

Before a capability row may move to `optimized`, the evidence chain must include:

- Baseline and candidate performance profiles.
- Before/after metrics.
- A correctness regression check.
- Rollback criteria.
- Profiler output linked to the capability row.

## MiniMax M3 Guidance

Use the Phase 3 tiny text slice as the first optimization surface for MiniMax M3.
Do not expand the slice to full multimodal, long-context, or distributed
execution until the smaller slice is proven.

Keep these MiniMax M3 optimization dimensions explicit:

- MiniMax Sparse Attention and any dense fallback.
- Long-context memory planning.
- Multimodal input contracts.
- Distributed communication behavior.
- Precision policy.
- Graph and compile behavior.
- Ascend NPU runtime readiness.

For Ascend NPU, preserve blocked runtime evidence from `docs/ops/ascend-npu-runtime.md`
until root-approved `npu-smi`, CANN activation, matching PyTorch/PTA or
`torch_npu`, tensor smoke, and the smallest VeOmni smoke all have evidence.

## Review Checklist

- [ ] The profile has a single backend-neutral top-level contract.
- [ ] Shared metrics include throughput, latency, memory, utilization, compile overhead, and runtime stability.
- [ ] Backend-specific counters live under a dedicated namespace.
- [ ] The exact manifest, recipe, and config references are preserved.
- [ ] The declared and actual workload shapes are both recorded.
- [ ] Optimization does not start without correctness-green evidence.
- [ ] Ascend NPU blockers are recorded as blocked evidence, not as support claims.
- [ ] MiniMax M3 keeps MSA, long-context, multimodal, distributed, precision, and NPU surfaces visible.
