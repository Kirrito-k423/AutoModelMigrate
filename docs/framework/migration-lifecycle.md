# Migration Lifecycle

## Purpose

The migration lifecycle tracks a migration from initial intake to production
readiness. A status transition is valid only when the required evidence exists.
This prevents "runs once" from being treated as migrated support.

## Lifecycle States

`Intake -> Gap Analysis -> Adapter Build -> Correctness -> Scale -> Optimize -> Accuracy Signoff -> Production Ready`

## State Details

### Intake

**Purpose:** Capture source, target, scope, first slice, non-goals, assumptions,
and initial evidence before implementation starts.

**Entry evidence**

- Migration request or case brief.
- Source and target framework/runtime identified.
- Model, algorithm, dataset, training feature, or inference feature named.

**Exit evidence**

- Migration manifest draft exists.
- Source and target references are pinned or marked as assumptions.
- First vertical slice and non-goals are recorded.
- Backend scope and validation gates are named.

**Allowed next states:** `Gap Analysis`

**Blocking conditions**

- No source/target distinction.
- First slice is not defined.
- Unsupported assumptions are hidden instead of recorded.

**MiniMax M3 example:** Phase 1 intake records `MiniMaxAI/MiniMax-M3` as source,
VeOmni as target, tiny text smoke as first slice, and NPU execution as blocked
until runtime gates pass.

### Gap Analysis

**Purpose:** Convert intake facts and assumptions into owner-layer work items.

**Entry evidence**

- Intake manifest or intake document exists.
- Fact/assumption log is available.
- Runtime evidence exists for targeted backends when backend support is in scope.

**Exit evidence**

- Gaps are grouped by owner layer.
- Every blocking gap has severity, evidence, first action, and blocking status.
- Non-goals and deferred items are explicit.

**Allowed next states:** `Adapter Build`, or back to `Intake` if source facts are
insufficient.

**Blocking conditions**

- Gaps lack owner layer or severity.
- Runtime blockers are mixed with model correctness failures.
- First action is vague or not executable.

**MiniMax M3 example:** Gap analysis names ModelSpec, FrameworkAdapter,
BackendAdapter, DataAdapter, ValidationSuite, and OptimizationLoop gaps, including
MSA support unknown and Ascend NPU runtime blocked.

### Adapter Build

**Purpose:** Implement or document the missing target-framework, backend, data,
recipe, and validation adapter surfaces required by the selected slice.

**Entry evidence**

- Gap analysis identifies blocking work.
- Adapter boundaries are clear.
- First slice acceptance gates exist.

**Exit evidence**

- Target framework can construct or route the selected slice, or reports a
  precise unsupported capability.
- Data fixtures and checkpoint/tokenizer contracts are available.
- Backend capability rows reflect the implementation state.

**Allowed next states:** `Correctness`

**Blocking conditions**

- Model code contains scattered accelerator-specific branches.
- Adapter work hides tokenizer, checkpoint, or processor assumptions.
- Backend runtime blockers are claimed as support.

**MiniMax M3 example:** VeOmni integration should either construct MiniMax M3 for
a tiny text smoke or report the exact FrameworkAdapter/DataAdapter/BackendAdapter
capability that blocks it.

### Correctness

**Purpose:** Prove semantic behavior before scale, performance, or accuracy
signoff work starts.

**Entry evidence**

- Adapter Build outputs exist.
- Smoke and parity recipes are declared.
- Fixtures and reference outputs or invariants exist.

**Exit evidence**

- Shape and dtype checks pass.
- Tokenizer/processor parity passes.
- Checkpoint inspection or load mapping is documented.
- Tiny forward, loss, or generation smoke matches declared invariants.

**Allowed next states:** `Scale`, `Optimize` only after required correctness
gates are green for the target path.

**Blocking conditions**

- Validation gates are missing.
- Drift thresholds are undefined.
- Runtime blocked paths are treated as failed correctness instead of blocked
  backend readiness.

**MiniMax M3 example:** The text-only tiny smoke may proceed on a reference path
while Ascend NPU remains blocked by `npu-smi`, CANN, and `torch_npu` gates.

### Scale

**Purpose:** Extend from tiny/single-device correctness to larger shapes, longer
context, multimodal fixtures, distributed recipes, or backend-specific scale
targets.

**Entry evidence**

- Correctness gates pass for the smaller path.
- Scale target is declared: device count, sequence/context length, batch, dtype,
  memory budget, and recipe.

**Exit evidence**

- Scale recipe runs or fails with a precise blocker.
- Memory, communication, and stability observations are recorded.
- Distributed mode and backend capability rows are updated.

**Allowed next states:** `Optimize`, `Accuracy Signoff`

**Blocking conditions**

- Scaling starts before tiny correctness passes.
- Hardware/runtime facts are missing.
- Distributed mode support is assumed without evidence.

**MiniMax M3 example:** Move from tiny text to 32K or 128K feasibility before any
1M context claim.

**Scale -> Optimize evidence chain:** For the declared slice, correctness must
stay green, the baseline performance profile must exist, and the candidate run
must reuse the same manifest, recipe, and workload identity before any tuning
claim starts.

### Optimize

**Purpose:** Improve performance using profiler evidence after correctness and
scale evidence exist.

**Entry evidence**

- Correctness gates pass for the path being optimized.
- Baseline performance report exists.
- Backend capability rows name native/emulated/operator status.

**Exit evidence**

- Profiling output identifies bottleneck.
- Hypothesis, change, config diff, before/after metrics, and rollback criteria
  are recorded.
- Regression guard exists.

**Allowed next states:** `Accuracy Signoff`, or back to `Correctness` if the
optimization changes semantics.

**Blocking conditions**

- Optimization begins before correctness.
- Performance change lacks profiler evidence.
- Kernel work hides semantic drift.

**MiniMax M3 example:** MSA performance comparison can begin only after dense,
emulated sparse, native sparse, or optimized sparse paths have correctness
evidence.

### Accuracy Signoff

**Purpose:** Prove task-level or fixture-level acceptance against declared
baselines and drift thresholds.

**Entry evidence**

- Correctness and relevant scale gates are complete.
- Accuracy baseline and threshold are declared.
- Dataset/task fixture is reproducible.

**Exit evidence**

- Accuracy metrics pass or gaps are recorded.
- Numerical drift is within threshold or explicitly accepted.
- Evaluation command, environment, and artifacts are saved.

**Allowed next states:** `Production Ready`

**Blocking conditions**

- No baseline.
- No threshold.
- Tokenizer/checkpoint drift is unresolved.

**MiniMax M3 example:** The baseline might be official Transformers output,
MiniMax API output, vLLM/SGLang, or an internal reference, but it must be pinned
before signoff.

### Production Ready

**Purpose:** Declare that the migrated path is safe for intended production or
team use.

**Entry evidence**

- Correctness, scale, optimization when applicable, accuracy, backend, and docs
  gates are complete.
- Known unsupported capabilities are explicit.
- Runbook and rollback information exist.

**Exit evidence**

- Final manifest and capability matrix are updated.
- Validation and performance reports are linked.
- Ownership and maintenance path are assigned.

**Allowed next states:** terminal for this migration version, or back to
`Intake` for a new scope/version.

**Blocking conditions**

- NPU/GPU support claim lacks backend evidence.
- Performance signoff is missing for performance-sensitive scope.
- Documentation does not separate supported, blocked, and unsupported paths.

**MiniMax M3 example:** Production Ready cannot be claimed for Ascend NPU until
root-level `npu-smi`, CANN, `torch_npu`, tensor smoke, VeOmni smoke, MiniMax M3
smoke, validation, and performance/accuracy gates satisfy the intended scope.

## Transition Rules

1. Every transition needs evidence, not just a task checkbox.
2. `Optimize` follows `Correctness`; it never substitutes for correctness.
3. `Accuracy Signoff` requires a declared baseline and drift/metric threshold.
4. `Production Ready` requires documentation, backend support, correctness,
   accuracy, and performance evidence for the claimed scope.
5. Backend readiness can be blocked independently while reference/model/framework
   work continues.
6. Any optimization that changes model semantics routes back to `Correctness`.
7. `Optimize -> Accuracy Signoff` requires the scoped correctness checks to stay
   green, before/after evidence to be recorded, and rollback criteria to be
   explicit.

## Status Reporting

Report status with:

- Current lifecycle state.
- Latest passed gate.
- Blocking gaps.
- Backend-specific readiness.
- Next action.
- Evidence links.

Example: `Correctness / GPU reference path pending tiny smoke / Ascend NPU blocked-runtime on CANN and torch_npu`.
