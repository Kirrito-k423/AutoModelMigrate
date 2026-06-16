# MiniMax M3 NPU Runtime Evidence

**Case:** VeOmni + MiniMax M3  
**Backend:** Ascend NPU, A2/910B-oriented path  
**Current status:** blocked before framework smoke  
**Shared runbook:** `docs/ops/ascend-npu-runtime.md`

This note ties the project-level Ascend runtime evidence to the MiniMax M3 case. It is intentionally conservative: the current host is not ready to claim VeOmni or MiniMax M3 NPU execution.

## Current Blocking Gates

| Gate | Required Before MiniMax M3 NPU Smoke | Current Status |
|------|--------------------------------------|----------------|
| Driver/DCMI | `npu-smi info` succeeds through the host-approved privilege path and device inventory is saved. | **blocked**: normal user gets DCMI `ret=-8005`; user reports root is required; root output has not been captured here. |
| CANN | CANN toolkit and kernels are installed or the validated VeOmni A2/910B Docker image is used. | **blocked**: `/usr/local/Ascend/ascend-toolkit` and `set_env.sh` are missing. |
| PTA/torch_npu | Matching PyTorch and `torch_npu`/PTA pair imports successfully. | **blocked**: `torch` and `torch_npu` are not installed. |
| Tensor smoke | A tiny tensor operation runs on NPU and returns to CPU correctly. | Not reached. |
| VeOmni smoke | Smallest VeOmni NPU recipe runs with captured logs and versions. | Not reached. |
| MiniMax M3 smoke | MiniMax M3 adapter can construct or run a tiny forward path on NPU. | Not reached; depends on all lower gates and model adapter gap analysis. |

## VeOmni A2/910B Baseline

The preferred NPU container starting point is the upstream VeOmni Ascend A2 Docker guide:

https://github.com/ByteDance-Seed/VeOmni/blob/main/docs/hardware_support/AscendDockerUsage/build_a2_docker.md

For this `aarch64` host, the relevant guide details are:

- Base image: `swr.cn-south-1.myhuaweicloud.com/ascendhub/cann:9.0.0-910b-ubuntu22.04-py3.11`.
- ARM64 Dockerfile: `docker/ascend/Dockerfile.ascend_9.0.0_a2.arm`.
- ARM64 dependency flow: `pip install -e .[npu_aarch64]`.
- Runtime device access: `/dev/davinci*`, `/dev/davinci_manager`, `/dev/devmm_svm`, `/dev/hisi_hdc`, Ascend driver libraries/tools, and add-ons.
- Large-model container option: `--shm-size=64G`.

Do not pull/build this image until root-level NPU visibility and network/disk budget are confirmed.

## Impact On BackendAdapter Design

MiniMax M3 should consume Ascend NPU support through BackendAdapter and capability-matrix data, not through scattered model-code branches.

| Capability | MiniMax M3 Need | BackendAdapter State To Represent |
|------------|-----------------|-----------------------------------|
| Device visibility | NPU devices must be visible before framework work. | `blocked: driver-permission-or-root-required` until root evidence is saved. |
| Runtime stack | CANN and `torch_npu` are prerequisites for VeOmni NPU. | `blocked: toolkit-missing` and `blocked: python-stack-missing` until gates pass. |
| MSA / sparse attention | MiniMax Sparse Attention is central to long-context efficiency. | `unsupported`, `emulated`, `native`, or `optimized` per backend after operator evidence. |
| Long context memory | 1M context is a case requirement, but not first smoke scope. | Stage support by context length and memory budget. |
| Precision | BF16/F32 artifact metadata must be mapped to supported NPU precision modes. | Declare supported dtype modes and drift tolerances per backend. |
| Profiling | Performance tuning follows correctness. | Profiler hooks become active only after smoke/parity gates pass. |

## Minimum Evidence Before Claiming NPU Support

1. Root-approved `npu-smi info` output captured.
2. CANN toolkit or VeOmni A2 Docker image route pinned with version and source.
3. PyTorch and `torch_npu` compatibility matrix recorded.
4. Gates 2-4 in `docs/ops/ascend-npu-runtime.md` pass.
5. VeOmni NPU smoke passes with logs.
6. MiniMax M3 tiny construction or forward smoke passes or reports a precise missing capability.

## Case Status

VeOmni + MiniMax M3 NPU execution is **blocked** at runtime setup. The next useful model work is still valid: complete the MiniMax M3 gap analysis, define ModelSpec/FrameworkAdapter needs, and record NPU-related gaps as BackendAdapter capability states.
