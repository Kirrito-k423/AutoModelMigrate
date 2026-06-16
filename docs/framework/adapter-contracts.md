# Adapter Contracts

## Purpose

Adapter contracts keep migration work from collapsing into one-off scripts. Each
layer owns a narrow responsibility, produces reviewable evidence, and consumes the
other layers through explicit contracts.

The main rule: accelerator-specific behavior belongs in BackendAdapter capability
data, not in model semantics or scattered model adapter conditionals.

## Contract Overview

| Contract | Owns | Does Not Own |
|----------|------|--------------|
| `ModelSpec` | Model/algorithm semantics independent of target framework and backend. | VeOmni builders, NPU runtime setup, profiler tuning. |
| `FrameworkAdapter` | Target framework hooks, model/config builders, recipes, checkpoint integration, distributed execution. | Backend kernel truth, tokenizer fixture truth, model architecture facts. |
| `BackendAdapter` | Device/runtime gates, precision, communication, memory/shape constraints, compile behavior, operators/kernels, profiler hooks. | Model architecture, target framework registration, dataset transforms. |
| `DataAdapter` | Tokenizer, processor, dataset schema, checkpoint metadata, fixtures, reference outputs. | Distributed execution, runtime installation, performance tuning. |
| `Recipe` | Reproducible commands and config surfaces for migration runs. | Claiming correctness without ValidationSuite evidence. |
| `ValidationSuite` | Smoke, parity, correctness, accuracy, drift, and transition gates. | Kernel optimization and profiler tuning. |
| `OptimizationLoop` | Baseline, profiling, bottleneck analysis, before/after metrics, regression guard, rollback. | Semantic correctness or accuracy signoff. |

## ModelSpec

**Responsibility:** Describe the model or algorithm independent of framework and
accelerator.

**Inputs**

- Source model docs, repository, config, artifact hashes, and papers.
- Fact/assumption log.
- Model dimensions: architecture, operators, modalities, context/shape limits,
  precision, checkpoint format, tokenizer/processor needs.

**Outputs**

- Manifest `model_spec`.
- Open assumptions that block signoff.
- Model capability requirements consumed by FrameworkAdapter and BackendAdapter.

**Prohibited ownership**

- Do not choose CANN/PTA versions.
- Do not encode GPU/NPU branches.
- Do not decide VeOmni model registration details.

**MiniMax M3 example**

ModelSpec records MiniMax Sparse Attention, advertised 1M context, native
text/image/video support, MoE scale, BF16/F32 metadata, and unknown checkpoint or
tokenizer details.

**Validation evidence**

- Source revision and hashes.
- Config parse evidence.
- Tokenizer/processor artifact inventory.
- Explicit assumptions for unknown MSA, checkpoint, and multimodal contracts.

## FrameworkAdapter

**Responsibility:** Integrate model/data contracts into the target framework.

**Inputs**

- ModelSpec requirements.
- DataAdapter tokenizer/processor/checkpoint contract.
- Target framework source/docs.
- Backend capability descriptors for runtime-specific constraints.

**Outputs**

- Target registration path.
- Builder/config policy.
- Remote-code or native-adapter decision.
- Training/inference/distributed recipe integration points.

**Prohibited ownership**

- Do not hide model semantics in target framework glue.
- Do not hard-code backend-specific operator choices in framework recipes.
- Do not claim tokenizer/checkpoint parity without DataAdapter evidence.

**MiniMax M3 example**

FrameworkAdapter decides where MiniMax M3 plugs into VeOmni and whether the first
slice wraps official Transformers remote code or creates a native VeOmni skeleton.
The decision is framework-local; MSA backend availability still belongs to
BackendAdapter.

**Validation evidence**

- VeOmni source file/symbol map.
- Construction or tiny smoke logs.
- Recipe command and config diff.

## BackendAdapter

**Responsibility:** Represent backend runtime and capability truth for CPU, GPU,
NPU, and future accelerators.

**Inputs**

- Runtime inspection output.
- Vendor installation/runtime docs.
- Framework backend docs.
- Operator/kernel support evidence.
- Profiler and benchmark evidence.

**Outputs**

- Backend capability matrix rows.
- Precision modes.
- Communication primitives.
- Memory and shape constraints.
- Graph/compile constraints.
- Runtime blockers and next actions.
- Profiler hooks.

**Prohibited ownership**

- Do not change model semantics to fit one accelerator.
- Do not mark support as native or optimized without evidence.
- Do not treat runtime blockers as capability maturity states.

**MiniMax M3 example**

Ascend NPU rows record root-only `npu-smi`, missing CANN, missing PyTorch/PTA
or `torch_npu`, and the upstream VeOmni A2/910B Docker guide as blockers/evidence.
MSA is a named capability with backend-specific maturity.

**Validation evidence**

- Driver/DCMI output.
- CANN and environment activation evidence.
- `torch_npu` import and tensor smoke.
- VeOmni NPU smoke.
- Operator parity and profiler reports.

## DataAdapter

**Responsibility:** Own the data and artifact boundary.

**Inputs**

- Source artifact tree.
- Tokenizer/processor files.
- Dataset samples.
- Checkpoint index and shard metadata.
- Reference implementation outputs.

**Outputs**

- Tokenizer and processor contract.
- Dataset schema and transform contract.
- Checkpoint mapping and loading assumptions.
- Tiny text/media fixtures.
- Reference outputs or invariants.

**Prohibited ownership**

- Do not choose accelerator capability status.
- Do not decide target framework registration.
- Do not treat unknown tokenizer/checkpoint facts as implementation details.

**MiniMax M3 example**

DataAdapter records tokenizer class, special tokens, chat template, media
placeholder tokens, processor output shapes, safetensors index, shard count,
module naming, and first text/image/video fixtures.

**Validation evidence**

- Fixture input and output records.
- Hashes for tokenizer/config/checkpoint index.
- Reference logits, shapes, dtypes, or generated text invariants.

## Recipe

**Responsibility:** Provide reproducible run surfaces for each lifecycle gate.

**Inputs**

- ModelSpec scope.
- FrameworkAdapter hooks.
- BackendAdapter capability and runtime constraints.
- DataAdapter fixtures.
- ValidationSuite requirements.

**Outputs**

- Commands/configs for artifact inspection, tiny smoke, single-device,
  distributed, accuracy, and performance runs.
- Backend, dtype, batch, sequence/context length, hardware, and environment
  activation fields.

**Prohibited ownership**

- Do not replace validation gates.
- Do not claim support because a command exists.
- Do not hide environment assumptions.

**MiniMax M3 example**

The first recipe should be artifact/config intake and tiny text smoke, not full
checkpoint load, full 1M context, multimodal forward, or NPU execution.

**Validation evidence**

- Exact command.
- Framework commit/revision.
- Backend/runtime versions.
- Logs and outputs.

## ValidationSuite

**Responsibility:** Decide what evidence is needed before status transitions.

**Inputs**

- Manifest assumptions.
- DataAdapter fixtures.
- Recipe commands.
- Backend capability state and blockers.
- Reference implementation outputs.

**Outputs**

- Smoke, unit, parity, correctness, accuracy, drift, runtime, and performance
  gates.
- Status transition blockers.
- Drift thresholds and acceptance criteria.

**Prohibited ownership**

- Do not perform kernel optimization.
- Do not weaken semantic gates to make performance work start earlier.
- Do not treat blocked runtime as failed model semantics.

**MiniMax M3 example**

ValidationSuite can allow reference/VeOmni artifact work to continue while Ascend
NPU remains blocked on runtime gates. It still blocks any NPU support claim until
driver, CANN, `torch_npu`, tensor smoke, and VeOmni smoke evidence exist.

**Validation evidence**

- Shape/dtype/checkpoint/tokenizer parity.
- Forward/loss parity where feasible.
- Task-level accuracy and drift thresholds.
- NPU gate outputs when backend support is claimed.

## OptimizationLoop

**Responsibility:** Optimize only after correctness and accuracy gates are explicit.

**Inputs**

- Validated recipes.
- Baseline metrics.
- Profiler traces.
- Backend capability matrix.
- Regression thresholds.

**Outputs**

- Bottleneck attribution.
- Optimization hypothesis.
- Config/code diff.
- Before/after throughput, latency, memory, utilization, compile overhead, and
  stability.
- Rollback criteria and regression guard.

**Prohibited ownership**

- Do not mask semantic drift with performance changes.
- Do not optimize unsupported or unvalidated behavior.
- Do not bypass BackendAdapter evidence for custom kernels.

**MiniMax M3 example**

OptimizationLoop later compares dense fallback, emulated sparse, native sparse,
and optimized sparse MSA paths, but only after correctness proves the selected
path is semantically safe.

**Validation evidence**

- Baseline report.
- Profiler output.
- Before/after metrics.
- Regression command and rollback path.

## Boundary Rules

1. Model semantics go in `ModelSpec`.
2. Target framework hooks go in `FrameworkAdapter`.
3. Accelerator differences go in `BackendAdapter` capability descriptors.
4. Tokenizer, processor, dataset, checkpoint, and fixtures go in `DataAdapter`.
5. Commands and configs go in `Recipe`.
6. Correctness, accuracy, drift, and transition gates go in `ValidationSuite`.
7. Profiling and performance changes go in `OptimizationLoop`.

If a change needs two layers, write the dependency explicitly instead of merging
the responsibilities.
