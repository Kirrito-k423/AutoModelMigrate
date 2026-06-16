# Backend Capability Matrix

## Purpose

The backend capability matrix records what each backend can support, what is
blocked, and what evidence is required before the migration can claim support.
It keeps GPU/NPU differences out of model code and makes backend readiness
reviewable.

## Capability Maturity

Capability maturity uses exactly these values:

| State | Meaning | Required Evidence |
|-------|---------|-------------------|
| `unsupported` | No usable path is known or the feature is intentionally absent. | Source docs, missing operator evidence, or explicit non-goal. |
| `emulated` | Fallback path exists but has caveats such as slower dense attention or CPU fallback. | Correctness/parity evidence plus performance caveat. |
| `native` | Backend/framework directly supports the capability. | Runtime logs, source docs, and correctness evidence. |
| `optimized` | Native path has profiler-backed tuning and regression guards. | Linked performance profile evidence, profiler output, before/after metrics, correctness regression check, and rollback criteria. |

Do not use `blocked` as a maturity state. Runtime blockers are separate fields.

## Runtime Blockers

Runtime blockers explain why a maturity claim cannot be validated yet.

| Blocker Field | Meaning | Example |
|---------------|---------|---------|
| `runtime_blocker.id` | Stable short identifier. | `driver-permission-or-root-required` |
| `runtime_blocker.reason` | Why execution or validation cannot proceed. | Normal-user `npu-smi info` fails. |
| `runtime_blocker.evidence` | Doc, command output, or run log proving the blocker. | `docs/ops/ascend-npu-runtime.md` |
| `runtime_blocker.next_action` | The next gate to close the blocker. | Capture root-approved `npu-smi info`. |

This lets a matrix row say:

| Backend | Capability | Maturity | Runtime Blocker |
|---------|------------|----------|-----------------|
| Ascend NPU | Device visibility | `unsupported` | `driver-permission-or-root-required` |
| Ascend NPU | CANN runtime | `unsupported` | `toolkit-missing` |
| Ascend NPU | PyTorch/PTA | `unsupported` | `python-stack-missing` |

## Matrix Dimensions

Every backend capability row should classify at least one of these dimensions:

| Dimension | Examples | Owner |
|-----------|----------|-------|
| Framework features | model registry, config builder, recipe launch, checkpoint hooks | FrameworkAdapter |
| Backend features | device visibility, runtime activation, dtype support, profiler hooks | BackendAdapter |
| Operators | dense attention, sparse attention, custom losses, quantization ops | BackendAdapter |
| Kernels | flash attention, block sparse attention, fused optimizer, vendor kernel | BackendAdapter |
| Precision modes | FP32, BF16, FP16, FP8, vendor-specific mixed precision | BackendAdapter |
| Communication primitives | all-reduce, reduce-scatter, all-gather, all-to-all, HCCL/NCCL | BackendAdapter |
| Memory constraints | HBM size, max batch, max context, KV cache behavior, activation checkpointing | BackendAdapter |
| Graph/compile constraints | dynamic shape, graph capture, compile cache, unsupported ops | BackendAdapter |
| Distributed modes | FSDP, tensor parallel, sequence parallel, expert parallel, pipeline parallel | FrameworkAdapter / BackendAdapter |
| Data artifacts | tokenizer, processor, checkpoint index, fixtures, reference outputs | DataAdapter |
| Validation gates | smoke, parity, accuracy, drift, performance readiness | ValidationSuite |
| Unsupported gaps | missing adapter, missing op, missing runtime, missing fixture | relevant owner layer |

## Row Shape

Use this row shape in manifests, docs, or issue trackers:

| Field | Required | Meaning |
|-------|----------|---------|
| `backend` | yes | Backend name, such as GPU, Ascend NPU, CPU. |
| `capability` | yes | Feature or runtime property being assessed. |
| `dimension` | yes | One matrix dimension from this doc. |
| `owner_layer` | yes | Contract responsible for closing the row. |
| `maturity` | yes | `unsupported`, `emulated`, `native`, or `optimized`. |
| `evidence` | yes | Project doc, external doc, source path, command output, or log. |
| `runtime_blockers` | no | Environment/runtime blockers separate from maturity. |
| `validation_targets` | yes | Smoke/parity/performance checks needed to claim support. |
| `next_action` | yes | First concrete action to improve the row. |
| `last_verified` | no | Date/time of the latest evidence check. |

## Backend Scope Checklist

Before claiming backend support, each row needs evidence for:

- Device visibility.
- Runtime stack activation.
- Python/framework adapter imports where applicable.
- Precision modes.
- Communication primitives.
- Memory and context/shape constraints.
- Graph/compile constraints.
- Operator/kernel availability.
- Profiler hooks when optimization is in scope.
- Validation target result.

## Ascend NPU Runtime Example

The current host has Ascend driver files, but it is not NPU-ready for VeOmni or
MiniMax M3 execution. Current blockers from `docs/ops/ascend-npu-runtime.md`:

| Capability | Dimension | Maturity | Runtime Blocker | Required Next Action |
|------------|-----------|----------|-----------------|----------------------|
| `npu-smi info` device visibility | Backend features | `unsupported` | `driver-permission-or-root-required` | Capture root-approved device inventory. |
| CANN toolkit activation | Backend features | `unsupported` | `toolkit-missing` | Confirm CANN version matrix and traffic/disk budget before official HiAscend download. |
| PyTorch/PTA import | Backend features | `unsupported` | `python-stack-missing` | Install a matching PyTorch and `torch_npu` pair after CANN route is pinned. |
| Tensor smoke | Validation gates | `unsupported` | depends on CANN and `torch_npu` | Run a tiny NPU tensor round trip. |
| VeOmni NPU smoke | Framework features | `unsupported` | depends on tensor smoke and VeOmni deps | Run the smallest VeOmni NPU recipe with logs. |

The upstream VeOmni Ascend A2/910B Docker guide remains the preferred container
reference before creating a custom recipe.

## MiniMax M3 Capability Examples

| Backend | Capability | Dimension | Maturity | Evidence | Runtime Blockers |
|---------|------------|-----------|----------|----------|------------------|
| GPU | Dense attention reference | Operators | `native` | PyTorch/reference path assumption from case intake | None recorded yet |
| GPU | MiniMax Sparse Attention | Operators / Kernels | `unsupported` | MSA support not inspected | implementation evidence missing |
| GPU | Tiny text optimization path | Graph/compile / Performance | `optimized` | `docs/framework/examples/veomni-minimax-m3.optimization-report.md` | None recorded yet |
| Ascend NPU | MiniMax Sparse Attention | Operators / Kernels | `unsupported` | NPU runtime blocked before operator evidence | root `npu-smi`, CANN, `torch_npu` |
| Ascend NPU | BF16 precision | Precision modes | `unsupported` | CANN/PTA matrix not pinned | toolkit and Python stack missing |
| Ascend NPU | HCCL distributed communication | Communication primitives | `unsupported` | No VeOmni NPU smoke yet | runtime gates 0-5 not passed |
| Ascend NPU | Long-context memory | Memory constraints | `unsupported` | No NPU memory or context evidence yet | runtime and MSA blockers |

## Matrix Review Rules

1. A row with `optimized` must link profiler evidence, before/after metrics, a
   correctness regression check, and rollback criteria.
2. A row with `native` must link correctness evidence.
3. A row with `emulated` must state the fallback and caveat.
4. A row with `unsupported` must name the blocker or non-goal.
5. Runtime blockers never replace maturity status.
6. Backend-specific branches are acceptable only behind BackendAdapter or a
   framework hook that consumes BackendAdapter data.
7. Review capability transitions against D-04-07, D-04-08, and D-04-09: keep
   backend differences in capability rows, require profiler-linked evidence
   before `optimized`, and keep Ascend NPU runtime blockers separate from
   maturity.

## Optimization Transition Notes

Use `optimized` only when the row can point to all of the following:

- A performance profile with baseline and candidate identity.
- A profiler capture for the declared workload.
- Before/after metrics for throughput, latency, memory, utilization, compile
  overhead, and runtime stability.
- A scoped correctness regression check.
- Rollback criteria.

If a backend is still blocked, keep the blocker in `runtime_blockers` or the
row evidence instead of forcing a maturity upgrade. That keeps GPU and NPU
comparable without pretending they have identical runtime states.
