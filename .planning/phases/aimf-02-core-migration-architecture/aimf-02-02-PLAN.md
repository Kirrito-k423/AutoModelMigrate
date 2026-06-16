---
phase: aimf-02-core-migration-architecture
plan: "02"
type: execute
wave: 1
depends_on: []
files_modified:
  - docs/framework/adapter-contracts.md
  - docs/framework/backend-capability-matrix.md
  - docs/framework/examples/veomni-minimax-m3-capabilities.md
autonomous: true
requirements:
  - ARCH-01
  - ARCH-03
  - ARCH-04
  - ACC-01
  - ACC-02
  - ACC-03
user_setup: []
must_haves:
  truths:
    - D-06: `ModelSpec`, `FrameworkAdapter`, `BackendAdapter`, `DataAdapter`, `Recipe`, `ValidationSuite`, and `OptimizationLoop` are stable architecture contracts.
    - D-07: FrameworkAdapter owns target framework hooks, builders, remote-code policy, recipes, and distributed execution hooks.
    - D-08: BackendAdapter owns device/runtime gates, precision, communication, memory/shape constraints, graph/compile constraints, operator/kernel availability, and profiler hooks.
    - D-09: DataAdapter owns tokenizer, processor, dataset, checkpoint, fixture, and reference-output contracts.
    - D-10: Capability maturity states are `unsupported`, `emulated`, `native`, and `optimized`; runtime blockers are separate evidence fields.
    - D-11: Sparse/custom operators such as MiniMax Sparse Attention are named capabilities with backend-specific maturity and evidence.
  artifacts:
    - docs/framework/adapter-contracts.md
    - docs/framework/backend-capability-matrix.md
    - docs/framework/examples/veomni-minimax-m3-capabilities.md
  key_links:
    - Backend design must reference `docs/ops/ascend-npu-runtime.md` and `docs/cases/veomni-minimax-m3/npu-runtime-evidence.md`.
---

<objective>
Define adapter boundaries and backend capability descriptors.

Purpose: Keep model semantics, framework integration, data artifacts, backend runtime facts, validation, and optimization independently owned so GPU/NPU differences do not leak into model logic.
Output: Adapter contract spec, backend capability matrix, and MiniMax M3 capability example.
</objective>

<context>
@.planning/phases/aimf-02-core-migration-architecture/02-CONTEXT.md
@.planning/phases/aimf-02-core-migration-architecture/aimf-02-RESEARCH.md
@.planning/phases/aimf-02-core-migration-architecture/aimf-02-VALIDATION.md
@.planning/REQUIREMENTS.md
@docs/cases/veomni-minimax-m3/gap-analysis.md
@docs/cases/veomni-minimax-m3/reusable-deltas.md
@docs/cases/veomni-minimax-m3/npu-runtime-evidence.md
@docs/ops/ascend-npu-runtime.md
</context>

<threat_model>
| Threat | Severity | Mitigation |
|--------|----------|------------|
| T-02-03 Adapter docs permit accelerator-specific branches inside model semantics. | high | Explicitly forbid model-code accelerator conditionals and route backend facts through BackendAdapter/capability descriptors. |
| T-02-04 Capability matrix uses optimistic labels without evidence. | high | Require every capability row to carry evidence, blocker, owner, and validation gate fields. |
</threat_model>

## Artifacts this phase produces

- New file: `docs/framework/adapter-contracts.md`
- New file: `docs/framework/backend-capability-matrix.md`
- New file: `docs/framework/examples/veomni-minimax-m3-capabilities.md`
- Contract names: `ModelSpec`, `FrameworkAdapter`, `BackendAdapter`, `DataAdapter`, `Recipe`, `ValidationSuite`, `OptimizationLoop`
- Capability state enum references: `unsupported`, `emulated`, `native`, `optimized`
- Backend evidence fields: `runtime_blockers`, `precision_modes`, `communication_primitives`, `memory_constraints`, `graph_compile_constraints`, `operator_kernels`, `profiler_hooks`

<tasks>

<task type="auto">
  <name>Task 1: Write adapter contract spec</name>
  <files>docs/framework/adapter-contracts.md</files>
  <read_first>.planning/phases/aimf-02-core-migration-architecture/02-CONTEXT.md, .planning/phases/aimf-02-core-migration-architecture/aimf-02-RESEARCH.md, docs/cases/veomni-minimax-m3/gap-analysis.md</read_first>
  <action>Create `docs/framework/adapter-contracts.md` with sections for each contract: ModelSpec, FrameworkAdapter, BackendAdapter, DataAdapter, Recipe, ValidationSuite, OptimizationLoop. For each contract, define responsibility, inputs, outputs, prohibited ownership, MiniMax M3 example, and validation evidence. Include an explicit rule: accelerator differences belong in BackendAdapter/capability data and must not be scattered through model adapter logic.</action>
  <verify>rg "ModelSpec|FrameworkAdapter|BackendAdapter|DataAdapter|Recipe|ValidationSuite|OptimizationLoop|prohibited|accelerator|capability" docs/framework/adapter-contracts.md</verify>
  <acceptance_criteria>
    - Each ARCH-01 contract has responsibility, inputs, outputs, and prohibited ownership.
    - The doc states model semantics are separated from distributed execution and accelerator implementation.
    - MiniMax M3 examples mention MSA, VeOmni hooks, DataAdapter tokenizer/checkpoint scope, and NPU runtime gates.
  </acceptance_criteria>
  <done>Adapter boundaries are explicit enough for future executors.</done>
</task>

<task type="auto">
  <name>Task 2: Define backend capability matrix</name>
  <files>docs/framework/backend-capability-matrix.md</files>
  <read_first>docs/framework/adapter-contracts.md, docs/ops/ascend-npu-runtime.md, docs/cases/veomni-minimax-m3/npu-runtime-evidence.md</read_first>
  <action>Create `docs/framework/backend-capability-matrix.md` with the maturity states `unsupported`, `emulated`, `native`, `optimized`; separate runtime blocker/evidence fields; and matrix dimensions for framework features, backend features, operators/kernels, precision modes, communication primitives, memory constraints, graph/compile constraints, distributed modes, profiler hooks, and unsupported gaps. Include a table showing how to record runtime blockers such as root-only `npu-smi`, missing CANN, and missing `torch_npu` without adding `blocked` to the maturity enum.</action>
  <verify>rg "unsupported|emulated|native|optimized|runtime blocker|npu-smi|CANN|torch_npu|precision|communication|memory|graph|compile|profiler|distributed" docs/framework/backend-capability-matrix.md</verify>
  <acceptance_criteria>
    - Capability matrix represents unsupported, emulated, native, and optimized states.
    - Runtime blockers are documented separately from maturity states.
    - Matrix includes ACC-02 dimensions: precision, communication, memory, graph/compile, and custom kernel availability.
    - Matrix includes ARCH-04 dimensions: framework features, backend features, operators, kernels, distributed modes, and unsupported gaps.
  </acceptance_criteria>
  <done>Backend capability matrix can represent GPU/NPU support without model forks.</done>
</task>

<task type="auto">
  <name>Task 3: Create MiniMax M3 capability example</name>
  <files>docs/framework/examples/veomni-minimax-m3-capabilities.md</files>
  <read_first>docs/framework/backend-capability-matrix.md, docs/cases/veomni-minimax-m3/intake.md, docs/cases/veomni-minimax-m3/npu-runtime-evidence.md</read_first>
  <action>Create `docs/framework/examples/veomni-minimax-m3-capabilities.md` with example capability rows for dense attention, MSA/block sparse attention, long-context memory, tokenizer/processor contract, checkpoint metadata, precision policy, VeOmni framework hooks, distributed recipe status, GPU reference path, and Ascend NPU path. For Ascend NPU, record root-only `npu-smi`, missing CANN, missing `torch_npu`, and VeOmni A2/910B Docker guide as blockers/evidence. Use `unsupported`, `emulated`, `native`, or `optimized` for maturity values.</action>
  <verify>rg "MiniMax M3|VeOmni|MSA|block sparse|long-context|tokenizer|checkpoint|precision|GPU|Ascend|npu-smi|CANN|torch_npu|Docker|unsupported|emulated|native|optimized" docs/framework/examples/veomni-minimax-m3-capabilities.md</verify>
  <acceptance_criteria>
    - Example contains at least one MiniMax M3 MSA capability row.
    - Example contains GPU and Ascend NPU backend paths.
    - Example does not claim current Ascend NPU execution support.
    - Example cites runtime evidence and VeOmni A2/910B Docker guide.
  </acceptance_criteria>
  <done>MiniMax M3 capability example demonstrates the matrix on a hard first case.</done>
</task>

</tasks>

<verification>
Before declaring plan complete:
- [ ] `rg "ModelSpec|FrameworkAdapter|BackendAdapter|DataAdapter|Recipe|ValidationSuite|OptimizationLoop" docs/framework/adapter-contracts.md`
- [ ] `rg "unsupported|emulated|native|optimized|runtime blocker" docs/framework/backend-capability-matrix.md`
- [ ] `rg "MiniMax M3|MSA|Ascend|CANN|torch_npu|VeOmni" docs/framework/examples/veomni-minimax-m3-capabilities.md`
- [ ] Confirm the docs separate model semantics from framework runtime and accelerator backend facts.
</verification>

<success_criteria>
- Adapter interfaces are defined without binding model logic to accelerator-specific code.
- Capability matrices can represent unsupported, emulated, native, and optimized states.
- NPU runtime blockers are represented as evidence/blockers, not hidden assumptions.
</success_criteria>

<output>
After completion, create `.planning/phases/aimf-02-core-migration-architecture/aimf-02-02-SUMMARY.md`
</output>
