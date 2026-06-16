# MiniMax M3 Reusable Template Deltas

**Source case:** VeOmni + MiniMax M3  
**Related docs:** `intake.md`, `assumptions.md`, `gap-analysis.md`, `npu-runtime-evidence.md`  
**Purpose:** Explain which reusable framework template fields were motivated by this first case and should carry into future migrations.

MiniMax M3 is intentionally a hard first case. Its sparse attention, long context, native multimodal input contract, large checkpoint scale, and accelerator portability needs pushed several fields into the reusable templates.

## Deltas Promoted To The Generic Intake Template

| Delta | Case Trigger | Why It Is Reusable |
|-------|--------------|--------------------|
| Fact versus assumption table | MiniMax M3 public details are recent and artifact details still need pinning. | Future migrations often start with incomplete docs, changing checkpoints, or unclear processor behavior. |
| Source/target framework split | VeOmni is the target path, while public examples use Transformers, vLLM, and SGLang. | Most migrations need to distinguish reference implementation from target integration. |
| Backend Scope | GPU reference and NPU execution have different runtime and kernel readiness. | Accelerator portability is a recurring AI Infra problem, not MiniMax-specific. |
| Custom operators / kernels | MiniMax Sparse Attention may require sparse/block attention support or explicit fallback status. | Other models can have rotary variants, fused kernels, quantization kernels, custom losses, or vendor ops. |
| Context / sequence / shape limits | The case advertises 1M context, but first validation must be staged. | Long context and dynamic shape support should be explicit across future migrations. |
| Modality / input contract | MiniMax M3 is native multimodal with text, image, and video. | Future data/model migrations need processor and modality contracts, even when the first slice is text-only. |
| Validation and Performance Gates | Running once is not enough for M3 because tokenizer, checkpoint, sparse attention, precision, and backend behavior can drift. | Every migration needs correctness, accuracy, and performance signoff. |
| Signoff section | NPU, accuracy, and performance readiness can be independently blocked. | Signoff must separate model correctness, backend support, and operational readiness. |

## Deltas Promoted To The Generic Gap Template

| Delta | Case Trigger | Why It Is Reusable |
|-------|--------------|--------------------|
| Owner Layer column | MiniMax M3 gaps span ModelSpec, FrameworkAdapter, BackendAdapter, DataAdapter, ValidationSuite, and OptimizationLoop. | Layer ownership prevents backend, data, model, and validation issues from collapsing into one ambiguous task list. |
| Severity column | Some gaps block the first slice, while others only block NPU or performance signoff. | Severity lets implementation planning focus on the smallest safe vertical slice. |
| First Action column | Each row needed a concrete next move: pin revision, inspect source, define capability state, or create fixture. | Future plans should be executable, not just descriptive. |
| Blocks Slice column | NPU runtime setup is blocked, but does not block a reference/VeOmni text smoke. | Blocking status prevents over-scoping the first slice while preserving hard gates for later claims. |
| Selected First Vertical Slice section | The case needed a small path that still tested core framework contracts. | Future migrations should pick a slice with rationale and non-goals before coding. |
| Non-goals section | Full checkpoint load, full 1M context, multimodal forward, distributed training, and NPU execution were explicitly deferred. | Non-goals protect the team from turning intake into an unbounded implementation phase. |

## Case-Specific Facts That Should Not Become Generic Requirements

| MiniMax M3 Fact | Keep As Case-Specific Because |
|-----------------|------------------------------|
| MiniMax Sparse Attention | The generic template should ask for custom attention/operators, not require MSA. |
| 1M context | The generic template should ask for context/shape limits, not require 1M. |
| Text/image/video native multimodal training | The generic template should ask for modality contracts, not require multimodal support. |
| Ascend A2/910B Docker guide | The generic template should ask for backend runtime evidence; the VeOmni A2 guide is a case/source-specific reference. |
| Current host root-only `npu-smi` behavior | The generic template should support privileged runtime evidence; this exact behavior belongs to this host. |

## Architecture Inputs For Phase 2

- ModelSpec needs fields for custom attention/operator requirements, advertised limits, staged validation limits, modality contracts, dtype policy, and artifact revision.
- FrameworkAdapter needs fields for target framework hooks, remote-code policy, data/tokenizer builder behavior, and recipe integration.
- BackendAdapter needs capability states such as unsupported, blocked, emulated, native, and optimized for GPU, NPU, and future accelerators.
- DataAdapter needs tokenizer, processor, dataset, checkpoint, and fixture evidence sections.
- ValidationSuite needs smoke/parity/accuracy gates that can distinguish `blocked-runtime` from `failed-correctness`.
- OptimizationLoop needs profiler evidence only after correctness gates pass.

## Future Migration Checklist

- Start with facts and assumptions, not code.
- Pin source artifacts before adapter work.
- Model accelerator differences as BackendAdapter capability states.
- Select a first vertical slice with blocking gaps and non-goals.
- Treat sparse or custom kernels as capabilities, not silent fallbacks.
- Stage long context and large checkpoint validation.
- Keep multimodal contracts visible even when first slice is text-only.
