# VeOmni + MiniMax M3 Capability Example

## Purpose

This example applies the backend capability matrix to the first migration case:
MiniMax M3 support through VeOmni. It is not a support claim. It records the
current capability state, evidence, blockers, and next action for each path.

## Capability Rows

| Backend | Capability | Dimension | Maturity | Evidence | Blockers | Next Action |
|---------|------------|-----------|----------|----------|----------|-------------|
| GPU | Dense attention reference path | Operators | `native` | `docs/cases/veomni-minimax-m3/intake.md` | Reference run not pinned | Pin reference framework revision and tiny fixture. |
| GPU | MiniMax Sparse Attention / MSA | Operators / Kernels | `unsupported` | `docs/cases/veomni-minimax-m3/assumptions.md` | Official implementation and fallback behavior unknown | Inspect MiniMax M3 source and identify native or emulated path. |
| GPU | Long-context memory | Memory constraints | `emulated` | Staged validation target in manifest example | Full 1M context not validated | Start tiny, then 32K, 128K, and 1M gates. |
| GPU | BF16/F32 precision policy | Precision modes | `native` | MiniMax M3 artifact metadata assumption | Exact dtype mix not pinned | Capture config and checkpoint dtype metadata. |
| GPU | VeOmni model registration | Framework features | `unsupported` | `docs/cases/veomni-minimax-m3/gap-analysis.md` | Exact VeOmni registry path unknown | Inspect VeOmni model/config/tokenizer builders. |
| Ascend NPU | Device visibility | Backend features | `unsupported` | `docs/ops/ascend-npu-runtime.md` | normal-user `npu-smi info` fails; root path needed | Capture root-approved `npu-smi info`. |
| Ascend NPU | CANN runtime | Backend features | `unsupported` | `docs/ops/ascend-npu-runtime.md` | CANN toolkit and `set_env.sh` missing | Confirm version matrix and traffic/disk budget, then use official HiAscend packages or VeOmni A2/910B Docker route. |
| Ascend NPU | PyTorch/PTA / `torch_npu` | Backend features | `unsupported` | `docs/ops/ascend-npu-runtime.md` | PyTorch and `torch_npu` missing | Install matching CANN/PTA/torch_npu pair after runtime route is pinned. |
| Ascend NPU | Dense attention tensor path | Operators | `unsupported` | NPU tensor smoke not reached | Depends on CANN and `torch_npu` | Run tiny tensor smoke, then framework smoke. |
| Ascend NPU | MiniMax Sparse Attention / MSA | Operators / Kernels | `unsupported` | `docs/cases/veomni-minimax-m3/npu-runtime-evidence.md` | Runtime stack blocked before operator evidence | Test only after CANN/PTA and VeOmni smoke pass. |
| Ascend NPU | Long-context memory | Memory constraints | `unsupported` | 1M context is advertised but unvalidated | NPU runtime, MSA, memory gates missing | Stage tiny, 32K, 128K, and 1M memory evidence. |
| Ascend NPU | BF16/F32 precision policy | Precision modes | `unsupported` | CANN/PTA matrix not pinned | Missing CANN and `torch_npu` | Record supported dtype policy after runtime stack install. |
| Ascend NPU | HCCL / distributed communication | Communication primitives | `unsupported` | No VeOmni NPU smoke yet | Runtime and distributed recipe gates missing | Verify HCCL/NPU distributed support after single-device smoke. |
| Ascend NPU | Graph/compile behavior | Graph/compile constraints | `unsupported` | No CANN environment active | Toolkit missing | Record dynamic-shape and compile constraints after CANN activation. |
| Ascend NPU | Profiler hooks | Profiler hooks | `unsupported` | Optimization is downstream of correctness | Runtime smoke and correctness gates missing | Enable profiler only after correctness passes. |

## Data And Framework Capabilities

| Owner Layer | Capability | Maturity | Evidence | Next Action |
|-------------|------------|----------|----------|-------------|
| DataAdapter | Tokenizer class and special tokens | `unsupported` | `docs/cases/veomni-minimax-m3/gap-analysis.md` | Inspect Hugging Face tokenizer files and chat template. |
| DataAdapter | AutoProcessor multimodal behavior | `unsupported` | `docs/cases/veomni-minimax-m3/intake.md` | Create tiny text fixture, then image/video fixture requirements. |
| DataAdapter | Checkpoint shard/index metadata | `unsupported` | `docs/cases/veomni-minimax-m3/assumptions.md` | Capture file list, safetensors index, dtype mix, and module naming. |
| FrameworkAdapter | VeOmni model construction hook | `unsupported` | `docs/cases/veomni-minimax-m3/gap-analysis.md` | Inspect VeOmni source for registry/build path. |
| FrameworkAdapter | VeOmni processor/tokenizer hook | `unsupported` | `docs/cases/veomni-minimax-m3/gap-analysis.md` | Compare official processor calls with VeOmni data builders. |
| ValidationSuite | Tiny reference/VeOmni smoke | `unsupported` | `docs/cases/veomni-minimax-m3/intake.md` | Define expected shape/dtype/output invariant. |
| OptimizationLoop | MSA performance comparison | `unsupported` | `docs/cases/veomni-minimax-m3/reusable-deltas.md` | Wait for correctness, then compare dense, emulated sparse, native sparse, optimized sparse. |

## Runtime Evidence Notes

- Current Ascend NPU support is blocked before framework smoke.
- `npu-smi info` is visible only through a root-approved path according to the
  user report; normal-user DCMI initialization fails.
- CANN must come from the official HiAscend community download page after the
  version matrix and traffic budget are confirmed.
- PyTorch/PTA and `torch_npu` must match the selected CANN route.
- For Ascend A2/910B Docker work, use VeOmni's upstream
  `docs/hardware_support/AscendDockerUsage/build_a2_docker.md` guide before
  writing a custom container recipe.

## Signoff Implications

- A `native` GPU dense attention path does not imply MSA support.
- An `unsupported` NPU runtime path does not block ModelSpec, DataAdapter, or
  VeOmni source inspection.
- A dense fallback for MSA can be marked `emulated` only after correctness
  gates prove semantic parity and performance caveats are documented.
- `optimized` requires profiler output, before/after metrics, and regression
  guards.
