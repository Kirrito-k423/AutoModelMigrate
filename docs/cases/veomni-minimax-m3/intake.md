# VeOmni + MiniMax M3 Migration Intake

**Case:** Add MiniMax M3 support through the VeOmni framework path  
**Status:** Phase 1 intake; not an implementation or runtime signoff  
**Primary target:** VeOmni  
**First model case:** MiniMax M3

This intake defines the first concrete migration case for the AI Infra Migration Framework. It turns public facts, current host evidence, and explicit assumptions into a scoped first slice that can drive gap analysis, adapter design, and validation planning.

## Scope

The case covers the work needed to migrate MiniMax M3 into a VeOmni-oriented workflow while keeping the reusable framework boundaries visible:

- Model architecture intake for MiniMax M3, including sparse attention, long context, multimodality, tokenizer, checkpoint, precision, and generation controls.
- VeOmni integration mapping for model construction, tokenizer/processor loading, data path, recipes, distributed/runtime configuration, and checkpoint handling.
- Backend scope for GPU and Ascend NPU, modeled through capabilities and gates instead of model-code forks.
- Validation scope for smoke, parity, accuracy, and performance evidence.

Out of scope for Phase 1: full training support, full 1M-context performance runs, kernel optimization, and a claim that the current host can already run VeOmni on NPU.

## Source/Target

| Dimension | Decision | Evidence |
|-----------|----------|----------|
| Source model | MiniMax M3 from `MiniMaxAI/MiniMax-M3` public artifacts. | https://huggingface.co/MiniMaxAI/MiniMax-M3; https://github.com/MiniMax-AI/MiniMax-M3 |
| Target framework | VeOmni is the first framework path. | https://github.com/ByteDance-Seed/VeOmni; https://veomni.readthedocs.io/en/latest/ |
| First accelerator path | GPU reference first where available; Ascend NPU is a gated backend path. | Project requirement plus current host evidence in `.planning/phases/01-veomni-minimax-m3-intake/01-RESEARCH.md`. |
| NPU container baseline | Upstream VeOmni Ascend A2/910B Docker guide should be the preferred starting recipe for A2/910B work. | https://github.com/ByteDance-Seed/VeOmni/blob/main/docs/hardware_support/AscendDockerUsage/build_a2_docker.md |

## Model Dimensions

| Area | Intake Fact Or Assumption | Migration Impact |
|------|---------------------------|------------------|
| Native multimodality | MiniMax M3 is described as a native multimodal model with text, image, and video support. | The first slice may be text-only, but processor/media token contracts must be inventoried before claiming model support. |
| Long context | Public sources describe 1M-token context support. | Validation must be staged: tiny smoke first, medium-context feasibility next, full 1M performance only after semantics pass. |
| MSA / sparse attention | MiniMax M3 introduces MiniMax Sparse Attention for long-context efficiency. | MSA must become an attention capability in ModelSpec/BackendAdapter, not a hidden dense fallback. |
| Parameter scale | Public model card reports about 428B total parameters and about 23B activated parameters. | Early work should use config inspection, meta construction, or reduced fixtures; full checkpoint load is not the first milestone. |
| Checkpoint | Exact shard layout, weight naming, dtype mix, and expert naming still require artifact inspection. | CheckpointAdapter work cannot begin safely until file list, index, and module-name mapping are recorded. |
| Tokenizer/processor | Hugging Face examples use `AutoProcessor` and multimodal chat templates with `trust_remote_code=True`. | VeOmni integration must decide whether to reuse Transformers processor logic or add explicit VeOmni-side processor hooks. |
| Precision | Hugging Face model metadata reports BF16/F32 tensors, but backend precision policy is not yet pinned. | Precision must be selected through backend capability and recipe configuration, especially on Ascend NPU. |
| Inference and training | Public local deployment examples focus on Transformers, vLLM, and SGLang; this project targets VeOmni first. | The first VeOmni slice should prove construction and forward semantics before full training or serving recipes. |

## VeOmni Extension Points

The first pass should inventory exact VeOmni files and symbols in Plan 01-02, but the extension surface to check is already clear:

| VeOmni Area | What To Inspect | Why It Matters |
|-------------|-----------------|----------------|
| Model build path | Model registry, config parsing, model/tokenizer builders, and remote-code policy. | Determines whether MiniMax M3 can be represented as configuration plus adapter or needs new model code. |
| Basic modules | Argument parsing, data builders, model/tokenizer builders, and training script conventions. | VeOmni docs expose these as recurring integration surfaces for recipes and tasks. |
| Multimodal data path | Dataset builders, media transforms, chat template handling, and processor calls. | M3 is multimodal; text-only smoke must not erase image/video requirements. |
| Attention implementation | Existing attention abstraction, flash/sparse hooks, sequence parallel assumptions, and mask layout. | MSA support may need backend-specific kernels or explicit unsupported/emulated status. |
| Distributed recipes | FSDP, tensor/sequence/expert parallel settings, checkpoint save/load, and launch scripts. | The case must later scale from smoke to distributed recipes without rewriting the model adapter. |
| Ascend support | NPU install docs, A2/910B Docker guide, `torch_npu` usage, and architecture-specific dependency flow. | The current project needs Ascend runtime gates before NPU execution claims. |

Sources to inspect during gap analysis:

- VeOmni basic modules: https://veomni.readthedocs.io/en/latest/usage/basic_modules.html
- VeOmni Ascend ARM install: https://veomni.readthedocs.io/en/latest/get_started/installation/install_ascend_arm.html
- VeOmni A2 Docker guide: https://github.com/ByteDance-Seed/VeOmni/blob/main/docs/hardware_support/AscendDockerUsage/build_a2_docker.md

## Backend Scope

Backends should be represented as capability descriptors:

| Capability | GPU Reference | Ascend NPU Path | Gate |
|------------|---------------|-----------------|------|
| Dense attention | Use the framework's known PyTorch/GPU path where available. | Requires `torch_npu` import and tensor smoke before framework work. | Correctness smoke. |
| MSA / block sparse attention | Determine whether official GPU kernels or PyTorch fallback exist. | Mark unsupported, emulated, native, or optimized only after CANN/PTA and operator evidence. | Capability matrix plus parity. |
| Long context memory | Start with tiny and medium fixtures before full-context runs. | Use A2/910B Docker/runtime guidance and memory gates after NPU readiness. | Performance feasibility. |
| Precision | Reference BF16/FP32 policy from model artifacts and framework recipes. | Match CANN/PTA supported dtype policy; do not assume parity. | Numeric drift threshold. |
| Profiling | Use PyTorch/VeOmni profiler output after correctness passes. | Add vendor profiler hooks only after Gate 0-5 runtime readiness. | Profiling report. |

Current Ascend host status is not NPU-ready for VeOmni execution: normal-user `npu-smi info` fails with DCMI initialization, the user reports root is required to see devices, CANN toolkit is absent, and `torch_npu` is not installed. This blocks framework smoke until runtime gates pass.

## Dataset/Checkpoint Scope

| Area | Required Intake | First Evidence |
|------|-----------------|----------------|
| Tokenizer and chat template | Tokenizer class, special tokens, media placeholders, BOS/EOS/PAD policy, thinking parameter behavior. | Artifact inspection of `MiniMaxAI/MiniMax-M3`. |
| Processor | Image/video preprocessing, supported message schema, media token alignment. | Compare official processor behavior with VeOmni data builders. |
| Checkpoint | File list, safetensors index, shard count, dtype, module naming, MoE expert naming, license. | Pin Hugging Face revision and compute config/index hashes. |
| Dataset fixture | Small text-only fixture first, then one image and one video fixture. | Golden fixture record with source input and expected processor output shape. |
| Baseline output | Official or reference implementation outputs/logits where feasible. | Transformers reference run on a small fixture before VeOmni parity. |

## Validation Gates

1. **Manifest gate:** Source revision, artifact hashes, license, backend target, and assumptions are recorded.
2. **Config gate:** MiniMax M3 config can be parsed into ModelSpec without full weight load.
3. **Tokenizer/processor gate:** Tokenizer and processor produce pinned outputs for tiny text and media fixtures.
4. **Checkpoint gate:** Weight index and module-name mapping are understood; reduced or meta construction path is defined.
5. **Model construction gate:** VeOmni can construct the model or a skeleton adapter with explicit unsupported capabilities.
6. **Forward/parity gate:** Tiny forward or generation smoke matches a reference within declared tolerances.
7. **Accuracy gate:** Task-level or fixture-level accuracy acceptance is declared before signoff.
8. **NPU gate:** Ascend Gate 0-5 runtime checks pass before any VeOmni+M3 NPU smoke is claimed.

## Performance Gates

Performance work starts only after correctness gates pass.

| Stage | Evidence |
|-------|----------|
| Baseline | Record reference framework, backend, dtype, batch, sequence length, and hardware. |
| Profiling | Capture time, memory, communication, attention cost, and bottleneck attribution. |
| Sparse/long-context | Compare dense, emulated sparse, native sparse, and optimized sparse modes where available. |
| NPU | Record CANN/PTA versions, A2/910B Docker or host route, device count, and profiler output. |
| Regression | Store before/after report and thresholds for future migrations. |

## Open Questions

- Which exact MiniMax M3 Hugging Face revision should be pinned for the first slice?
- Is MiniMax's MSA implementation directly reusable, or does VeOmni need a local abstraction and backend-specific kernels later?
- Can VeOmni construct MiniMax M3 through existing Transformers/remote-code paths, or is a native model adapter required?
- What is the minimum useful medium-context gate: 32K, 128K, or another team-standard length?
- Which accuracy baseline should be authoritative: official Transformers run, MiniMax API output, vLLM/SGLang serving, or internal reference?
- What is the approved privileged path for root-only `npu-smi info` evidence on this host?
- Which CANN/PTA matrix should be pinned before building or pulling the VeOmni A2/910B image?

## First Vertical Slice Candidate

**Recommended first slice:** MiniMax M3 artifact and config intake -> VeOmni model/processor gap analysis -> text-only tiny construction or forward smoke with explicit MSA/multimodal/NPU capability statuses.

Why this slice:

- It proves the migration framework's intake, ModelSpec, FrameworkAdapter, DataAdapter, BackendAdapter, and ValidationSuite boundaries without requiring full 428B checkpoint execution.
- It forces MiniMax M3-specific risks into the open: MSA, 1M context, multimodal processor contract, checkpoint naming, precision, and backend support.
- It keeps Ascend NPU honest by documenting NPU as gated until root-level DCMI evidence, CANN, `torch_npu`, tensor smoke, and VeOmni smoke pass.

First implementation should stop at a clear result: either a tiny reference/VeOmni smoke passes, or the gap analysis names the exact missing adapter/backend capability that blocks it.

## Requirement Links

- `M3-01`: Captures MiniMax M3 model dimensions, facts, assumptions, and validation risks.
- `M3-02`: Defines the first VeOmni extension surfaces and vertical slice candidate for gap analysis.
