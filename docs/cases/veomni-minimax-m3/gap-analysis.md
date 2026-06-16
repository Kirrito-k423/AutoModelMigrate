# VeOmni + MiniMax M3 Gap Analysis

**Case:** MiniMax M3 support through VeOmni  
**Inputs:** `intake.md`, `assumptions.md`, `npu-runtime-evidence.md`  
**Status:** Phase 1 gap analysis; not implementation signoff

This gap analysis converts the case intake into owner-layer work items. The selected first vertical slice is intentionally small enough to execute, but still meaningful enough to test the migration framework's core contracts: ModelSpec, FrameworkAdapter, BackendAdapter, DataAdapter, ValidationSuite, and OptimizationLoop.

## Severity Scale

| Severity | Meaning |
|----------|---------|
| Critical | Blocks the first slice or can create false migration success. |
| High | Must be resolved before correctness signoff, but may not block initial artifact inspection. |
| Medium | Needed before scale, NPU, multimodal, or performance work. |
| Low | Helpful hardening or documentation after the first slice. |

## ModelSpec Gaps

| Gap | Evidence | Severity | Owner Layer | Proposed First Action | Blocks Slice |
|-----|----------|----------|-------------|-----------------------|--------------|
| MiniMax M3 config schema is not pinned. | Intake requires source revision, config hash, tokenizer hash, and license; assumptions note public artifacts may change. | Critical | ModelSpec | Pin Hugging Face revision and capture `config.json`, remote-code class names, dtype metadata, and license. | Yes |
| MSA must be represented as a model capability. | MiniMax M3 introduces MiniMax Sparse Attention for long context; dense fallback would hide memory/performance failure. | Critical | ModelSpec | Add an MSA field to the case ModelSpec draft: attention type, block layout unknowns, kernel requirement, fallback status. | Yes |
| 1M context needs staged representation. | Public sources describe 1M context, but full-context tests are expensive and not first-smoke friendly. | High | ModelSpec | Represent maximum advertised context separately from first-slice context gates such as tiny, 32K, 128K, and full 1M. | No |
| Native multimodality must not disappear in a text-only slice. | Intake states text-only smoke may be first, but image/video processor contracts must be inventoried. | High | ModelSpec | Record modality support, media token assumptions, and deferred image/video gates in the case spec. | No |
| MoE/activated parameter semantics need artifact confirmation. | Public model card reports about 428B total and about 23B activated parameters, but exact expert layout is not inspected. | Medium | ModelSpec | Capture MoE/expert fields from model config and checkpoint index before any load mapping. | No |

## FrameworkAdapter Gaps

| Gap | Evidence | Severity | Owner Layer | Proposed First Action | Blocks Slice |
|-----|----------|----------|-------------|-----------------------|--------------|
| Exact VeOmni model registration path is unknown. | Intake lists model registry, config parsing, model/tokenizer builders, and remote-code policy as extension points. | Critical | FrameworkAdapter | Inspect VeOmni source for model builder/registry and document where MiniMax M3 would plug in. | Yes |
| Remote-code and Transformers integration policy is undecided. | Hugging Face usage examples rely on `AutoProcessor`, `AutoModelForMultimodalLM`, and `trust_remote_code=True`. | Critical | FrameworkAdapter | Decide whether the first slice wraps official Transformers remote code or builds a native VeOmni adapter skeleton. | Yes |
| VeOmni processor/tokenizer builder compatibility is unknown. | M3 processor uses multimodal chat-template behavior; VeOmni data/model builder hooks need mapping. | High | FrameworkAdapter | Compare official processor calls with VeOmni tokenizer/data builder interfaces and list adapter shims. | Yes |
| Attention implementation hook is unknown. | MSA may require sparse/block attention support or explicit unsupported/emulated status. | High | FrameworkAdapter | Identify VeOmni attention abstraction and where backend capability status should be consumed. | No |
| Distributed recipe requirements are not scoped. | Intake names FSDP, tensor/sequence/expert parallel, and checkpoint save/load as future scale surfaces. | Medium | FrameworkAdapter | Mark distributed training as a non-goal for first slice; record recipe fields needed later. | No |

## BackendAdapter Gaps

| Gap | Evidence | Severity | Owner Layer | Proposed First Action | Blocks Slice |
|-----|----------|----------|-------------|-----------------------|--------------|
| Ascend driver/DCMI evidence is incomplete for current user. | Normal-user `npu-smi info` fails with DCMI `ret=-8005`; root output has not been captured here. | High | BackendAdapter | Record current state as `blocked: driver-permission-or-root-required`; require root-approved Gate 0 evidence before NPU smoke. | No |
| CANN toolkit is missing. | `/usr/local/Ascend/ascend-toolkit` and `set_env.sh` are absent. | High | BackendAdapter | Pin CANN route: host install from HiAscend or VeOmni A2/910B Docker image after traffic budget confirmation. | No |
| PyTorch and `torch_npu` are missing. | Inspector reports `torch` and `torch_npu` modules not found. | High | BackendAdapter | Select a CANN/PyTorch/torch_npu compatibility matrix before installing. | No |
| MSA backend support is unknown on GPU and NPU. | MiniMax M3's MSA is central to long-context efficiency; NPU operator support has not been tested. | Critical | BackendAdapter | Add capability states for MSA: unsupported, emulated, native, optimized; first slice may mark NPU as blocked. | Yes |
| Precision policy is not pinned per backend. | Artifact metadata reports BF16/F32, while NPU dtype support must come from CANN/PTA matrix. | High | BackendAdapter | Record allowed dtypes and drift expectations per backend in capability matrix. | Yes |
| VeOmni A2/910B Docker build is not verified locally. | Upstream guide exists, but image pull/build may consume large traffic and disk; root/device access still needs proof. | Medium | BackendAdapter | Treat upstream guide as reference, not completed setup; add a future Docker smoke checklist. | No |

## DataAdapter Gaps

| Gap | Evidence | Severity | Owner Layer | Proposed First Action | Blocks Slice |
|-----|----------|----------|-------------|-----------------------|--------------|
| Tokenizer class and special tokens are not inventoried. | Assumptions mark tokenizer type, special tokens, chat template, BOS/EOS/PAD, and media tokens as unknown. | Critical | DataAdapter | Inspect Hugging Face tokenizer files and record class, vocab size, special tokens, and chat template hash. | Yes |
| Processor behavior is not pinned. | Hugging Face examples use `AutoProcessor` and multimodal message content. | High | DataAdapter | Create tiny text fixture and record processor output fields/shapes before VeOmni adaptation. | Yes |
| Media preprocessing is not scoped for image/video. | MiniMax M3 is native multimodal, but first slice may be text-only. | Medium | DataAdapter | Defer image/video execution but record one image and one video fixture requirement for later gates. | No |
| Checkpoint shard/index layout is not inspected. | Assumptions note exact sharding, safetensors layout, dtype, and module-name mapping need verification. | Critical | DataAdapter | Capture file list, safetensors index, shard count, dtype mix, and module name patterns from pinned revision. | Yes |
| Reference baseline output is not selected. | Intake asks whether baseline should be Transformers, MiniMax API, vLLM/SGLang, or internal reference. | High | DataAdapter | Use official Transformers path as the first reference if artifact size permits; otherwise use config/tokenizer-only fixtures first. | Yes |

## ValidationSuite Gaps

| Gap | Evidence | Severity | Owner Layer | Proposed First Action | Blocks Slice |
|-----|----------|----------|-------------|-----------------------|--------------|
| First-slice acceptance thresholds are not written. | Intake defines validation gates but no drift thresholds or expected outputs yet. | Critical | ValidationSuite | Define tiny fixture expected outputs or shape/dtype invariants before claiming a smoke pass. | Yes |
| Reference/VeOmni parity harness does not exist. | Migration completion requires correctness, accuracy, and performance evidence, not runnable code. | Critical | ValidationSuite | Create a validation plan for reference processor/model output versus VeOmni adapter output. | Yes |
| NPU gates are not connected to model validation status. | NPU evidence shows runtime blockers before framework smoke. | High | ValidationSuite | Add a rule: NPU status can be `blocked-runtime` while model intake proceeds on CPU/GPU/reference path. | No |
| Multimodal accuracy fixtures are not selected. | M3 includes text, image, and video, but no acceptance set exists. | Medium | ValidationSuite | Pick one text-only fixture now and queue image/video fixtures for later validation plan. | No |
| Performance signoff thresholds do not exist. | Intake states performance starts after correctness. | Medium | ValidationSuite | Add performance gates as non-blocking for first slice and required for later optimize state. | No |

## OptimizationLoop Gaps

| Gap | Evidence | Severity | Owner Layer | Proposed First Action | Blocks Slice |
|-----|----------|----------|-------------|-----------------------|--------------|
| Baseline profiling configuration is undefined. | Intake requires baseline, profiler, sparse/long-context, NPU, and regression evidence. | Medium | OptimizationLoop | Define the minimum report fields now, but defer profiling until correctness passes. | No |
| MSA performance modes are not comparable yet. | MSA may be unsupported, emulated, native, or optimized per backend. | Medium | OptimizationLoop | Record future comparison axes: dense fallback, emulated sparse, native sparse, optimized sparse. | No |
| Long-context memory gates are not staged. | Full 1M context is not first-slice scope. | Medium | OptimizationLoop | Define future memory stages: tiny, 32K, 128K, 1M, with hardware and dtype recorded. | No |
| NPU profiler route is not selected. | CANN/PTA runtime is not installed and VeOmni smoke has not run. | Low | OptimizationLoop | Defer vendor profiler selection until Gate 5 VeOmni NPU smoke passes. | No |

## Requirements Trace

- `M3-02`: VeOmni extension gaps are mapped to FrameworkAdapter work.
