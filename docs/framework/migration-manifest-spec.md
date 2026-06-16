# Migration Manifest Specification

## Purpose

The migration manifest is the first contract for a model, framework, dataset,
training feature, inference feature, or accelerator migration. It records what is
known, what is assumed, who owns each layer, and what evidence is required before
the migration can advance.

This spec is intentionally schema-first. Humans review this document; automation
uses `docs/framework/schemas/migration-manifest.schema.json`; case examples use
`docs/framework/examples/`.

## Top-Level Manifest

Every manifest has these top-level sections:

| Section | Owner | Purpose |
|---------|-------|---------|
| `migration` | cross-layer | Identity, lifecycle status, first slice, non-goals, and assumptions. |
| `source` | FrameworkAdapter / DataAdapter | Source framework, runtime, checkpoint, or model artifact reference. |
| `target` | FrameworkAdapter | Target framework/runtime that will receive support. |
| `model_spec` | ModelSpec | Model or algorithm semantics independent of framework/backend. |
| `framework_adapter` | FrameworkAdapter | Target framework hooks and execution integration points. |
| `backend_matrix` | BackendAdapter | Accelerator/runtime/operator capability rows. |
| `data_contract` | DataAdapter | Tokenizer, processor, dataset, checkpoint, fixtures, and reference outputs. |
| `recipe` | Recipe | Reproducible commands for smoke, single-device, distributed, serving, or training runs. |
| `validation` | ValidationSuite | Smoke, parity, accuracy, drift, and status gates. |
| `optimization` | OptimizationLoop | Profiling evidence, optimization hypotheses, before/after metrics, and rollback rules. |
| `ownership` | cross-layer | Team/reviewer assignment by manifest section. |
| `evidence` | cross-layer | Source docs, command outputs, file hashes, and run logs. |

## Owner Layers

The architecture contracts are stable across migrations:

- `ModelSpec`: model or algorithm semantics that should not depend on one
  trainer, serving stack, or accelerator.
- `FrameworkAdapter`: target framework registration, model/config builders,
  tokenizer/processor hooks, recipes, checkpoints, and distributed execution
  integration.
- `BackendAdapter`: accelerator runtime gates, precision modes, communication
  primitives, memory/shape constraints, graph/compile constraints, operators,
  kernels, and profiler hooks.
- `DataAdapter`: tokenizer, processor, dataset schema, media transforms,
  checkpoint files, fixture generation, and reference outputs.
- `Recipe`: reproducible command/config surfaces for smoke, correctness,
  scale, and performance runs.
- `ValidationSuite`: smoke, parity, correctness, accuracy, drift, and status
  transition gates.
- `OptimizationLoop`: baseline, profiler evidence, hypothesis, config diff,
  before/after metrics, regression guard, and rollback criteria.

## ModelSpec

`model_spec` records model semantics before framework or backend implementation
choices. It includes:

- `model_family`: model or algorithm family.
- `architecture`: high-level architecture facts and open assumptions.
- `modalities`: input/output modalities such as text, image, video, audio, or
  tabular data.
- `attention`: named attention/operator requirements, including custom or sparse
  variants.
- `context.advertised_limit`: the public or source-claimed maximum context,
  sequence, batch, or shape limit.
- `context.validation_targets`: staged gates that prove progressively larger
  shapes. Advertised limits and validation targets are separate because a model
  can advertise 1M context while the first slice proves only tiny or medium
  fixtures.
- `precision_policy`: declared dtype and mixed-precision expectations.
- `artifact_revision`: pinned source revision, hash, or artifact evidence.
- `checkpoint`: checkpoint format, sharding, dtype mix, naming, and known gaps.
- `tokenizer_processor`: tokenizer, processor, chat template, and media-token
  contract.
- `assumptions`: unresolved facts that must be validated before signoff.

MiniMax M3 example values:

- `attention.name`: `MiniMax Sparse Attention`
- `context.advertised_limit`: `1M tokens`
- `context.validation_targets`: `tiny`, `32K`, `128K`, `1M`
- `modalities`: `text`, `image`, `video`
- `precision_policy`: `BF16`, `F32` until backend evidence pins more detail
- `checkpoint`: public artifact layout unknown until revision/index inspection

## FrameworkAdapter

`framework_adapter` records how the target framework consumes the model and data
contract. It owns:

- Model registration and construction hooks.
- Config parsing and remote-code policy.
- Tokenizer/processor builder integration.
- Dataset and transform builder integration.
- Checkpoint load/save hooks.
- Training, inference, and distributed recipe surfaces.

For VeOmni + MiniMax M3, this layer decides where MiniMax M3 plugs into VeOmni
and whether the first slice wraps official Transformers remote code or starts a
native VeOmni skeleton.

## BackendAdapter And Backend Matrix

Backend support is captured in `backend_matrix`, not scattered through model
logic. Each row names:

- `backend`: GPU, Ascend NPU, CPU, or future accelerator.
- `capability`: feature being assessed, such as dense attention, MSA, BF16,
  communication, graph compile, long-context memory, or profiler hooks.
- `status`: capability maturity.
- `evidence`: source docs, command output, logs, benchmark records, or issue
  references.
- `blockers`: runtime, operator, version, access, or evidence gaps.
- `validation_targets`: checks that prove the row is safe to claim.

## Capability Status Semantics

Capability maturity values are exactly:

| Status | Meaning |
|--------|---------|
| `unsupported` | No usable path is known or expected for this backend/capability. |
| `emulated` | A fallback exists but may not meet performance, memory, or parity targets. |
| `native` | Backend/framework provides direct support with correctness evidence. |
| `optimized` | Native support has profiler-backed tuning and regression guards. |

Do not add `blocked` to this enum. Runtime readiness blockers are captured in
`blockers` and `evidence`. This lets the manifest say "Ascend NPU MSA maturity is
unsupported or unknown, and current execution is blocked by missing CANN" without
confusing capability maturity with environment readiness.

## Runtime Blockers

Runtime blockers describe why a claimed capability cannot be validated yet.
Examples:

- `driver-permission-or-root-required`: normal-user `npu-smi info` fails, root
  evidence still needs to be captured.
- `toolkit-missing`: CANN toolkit is absent.
- `python-stack-missing`: PyTorch/PTA or `torch_npu` is not installed.
- `operator-evidence-missing`: custom kernel/operator availability has not been
  proven.

Each blocker needs an owner layer, evidence reference, and next action.

## Data Contract

`data_contract` covers tokenizer, processor, dataset, checkpoint, fixture, and
reference-output evidence. For MiniMax M3, the first useful records are:

- Tokenizer class, vocab size, special tokens, chat template, BOS/EOS/PAD policy.
- Media placeholder tokens and image/video processor behavior.
- Checkpoint file list, safetensors index, shard count, dtype mix, and module
  naming.
- Tiny text fixture first, then one image and one video fixture.
- Reference outputs or invariants from a pinned implementation.

## Recipe

`recipe` names reproducible commands and config surfaces. It should distinguish:

- Config/artifact inspection.
- Tiny smoke.
- Single-device correctness.
- Distributed scale.
- Accuracy evaluation.
- Performance profiling.

Recipes should record backend, dtype, batch, sequence/context length, hardware,
framework revision, and required environment activation.

## Validation

`validation` defines what blocks lifecycle transitions. It should include:

- Manifest/config gate.
- Tokenizer/processor gate.
- Checkpoint gate.
- Construction gate.
- Forward/parity gate.
- Accuracy/drift gate.
- Backend runtime gate.
- Performance gate when the lifecycle reaches optimization.

## Optimization

`optimization` is downstream of correctness and accuracy contracts. It records:

- Baseline command and metrics.
- Profiler evidence.
- Bottleneck hypothesis.
- Change and config diff.
- Before/after throughput, latency, memory, utilization, compile overhead, and
  stability.
- Regression guard and rollback criteria.

The optimization section should also point to concrete artifacts:

- `performance_profile`: the exact performance profile file or URI.
- `optimization_report`: the exact optimization report file or URI.
- `backend_capability_rows`: the backend capability entries the change touches.
- `correctness_result`: the scoped correctness result that stayed green.
- `backlog_item`: the unresolved bottleneck item, if the attempt did not close
  the loop.

Kernel optimization is not a substitute for parity. A sparse attention backend
can be optimized only after correctness and baseline evidence exists.

## Ownership And Evidence

Every section should carry:

- `owner_layer`: the architecture contract responsible for the section.
- `status`: maturity or lifecycle status.
- `evidence`: source docs, command output, file hashes, run logs, or issue links.
- `blockers`: explicit gaps that prevent support claims.
- `assumptions`: facts that must be verified before signoff.

## MiniMax M3 Example Mapping

| Manifest Area | MiniMax M3 Example |
|----------------|--------------------|
| Source | `MiniMaxAI/MiniMax-M3` public artifacts. |
| Target | VeOmni framework path. |
| ModelSpec attention | MiniMax Sparse Attention / MSA. |
| ModelSpec context | Advertised 1M context, staged tiny/32K/128K/1M gates. |
| ModelSpec modality | Native text, image, and video support. |
| Backend matrix | GPU reference path plus Ascend NPU gated path. |
| Data contract | Tokenizer/processor and checkpoint layout still require artifact inspection. |
| Validation | Tiny text smoke before multimodal, distributed, long-context, or NPU claims. |
| Optimization | MSA and long-context profiling only after correctness gates pass. |

## Review Checklist

- [ ] Every top-level section has owner and evidence.
- [ ] Capability maturity uses only `unsupported`, `emulated`, `native`, or
  `optimized`.
- [ ] Runtime blockers are separate from maturity states.
- [ ] Advertised model limits are separate from validation targets.
- [ ] Source and target references are pinned or explicitly marked as assumptions.
- [ ] MiniMax M3 example values keep MSA, long context, multimodality, checkpoint,
  precision, and NPU blockers visible.
- [ ] Optimization links to concrete performance profile and optimization
  report artifacts.
