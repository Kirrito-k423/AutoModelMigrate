# Ascend NPU Runtime Readiness

**Purpose:** Keep Ascend NPU setup repeatable for VeOmni + MiniMax M3 and future AI Infra migration cases.  
**Current host status:** `driver-permission-or-root-required`  
**Evidence time:** 2026-06-16 UTC  
**Skill source:** `$HOME/.codex/skills/ascend-npu-runtime`

This document is an evidence record and runbook. It does not claim that the current host can run VeOmni or MiniMax M3 on NPU yet.

## Current Host Evidence

Read-only inspector command:

```bash
python3 $HOME/.codex/skills/ascend-npu-runtime/scripts/inspect_npu_env.py >/tmp/ascend-npu-runtime.json
```

| Layer | Evidence | Interpretation |
|-------|----------|----------------|
| OS | Ubuntu 22.04.4 LTS, kernel `5.15.0-94-generic`, architecture `aarch64`. | Use ARM64/aarch64 packages and Dockerfiles. |
| Driver files | `/usr/local/Ascend` and `/usr/local/Ascend/driver` exist. `/usr/local/Ascend/driver/version.info` reports `Version=25.5.2`, `ascendhal_version=7.35.23`, and compatible firmware `[7.0.0,8.9.9]`. | Driver artifacts are present. |
| `npu-smi` path | `/usr/local/sbin/npu-smi`. | CLI exists. |
| Normal-user `npu-smi info` | Fails with `DrvMngGetConsoleLogLevel failed. (ret=4)` and `dcmi module initialize failed. ret is -8005`. | Do not treat the driver as proven usable from the project user. |
| Privileged `npu-smi info` | Non-interactive `sudo -n /usr/local/sbin/npu-smi info` fails with `sudo: a password is required`; the user reported `npu-smi info` is visible only after switching to root. | Root or an approved privileged path is required to capture Gate 0 evidence. |
| CANN toolkit | `/usr/local/Ascend/ascend-toolkit` missing; `set_env.sh` missing. | CANN Gate 1 is blocked. |
| Environment | `ASCEND_HOME_PATH`, `ASCEND_TOOLKIT_HOME`, `LD_LIBRARY_PATH`, and `PYTHONPATH` are empty in the current shell. | CANN runtime is not activated. |
| Python stack | Python 3.10.12 exists; `torch` and `torch_npu` modules are not installed. | PTA/torch_npu Gate 2 is blocked. |

## Install Route

Prefer a project-approved container or base image for shared NPU servers. Use a host install only when system-level changes are approved for this machine.

Before downloading anything, pin the version matrix:

| Layer | Version To Resolve |
|-------|--------------------|
| Driver/firmware | Current driver evidence is `25.5.2`; root-level `npu-smi info` still needs capture. |
| CANN | Toolkit and kernels from the official HiAscend community download page, matching the driver branch. |
| Python | Current host Python is 3.10.12; VeOmni A2 Docker guide uses Python 3.11 in its CANN image. |
| PyTorch | Must match the chosen CANN and torch_npu/PTA release. |
| PTA/torch_npu | Install only from an official or project-pinned compatibility matrix. |
| Framework | VeOmni commit/version and NPU dependency extra. |

CANN packages must come from the official HiAscend community download page: https://www.hiascend.com/developer/download/community/result

These downloads are large. Confirm the exact CANN version and traffic/disk budget before fetching packages or pulling images.

## VeOmni A2/910B Docker Reference

Use the upstream VeOmni Ascend A2 Docker guide as the first Docker recipe for Ascend A2/910B work:

https://github.com/ByteDance-Seed/VeOmni/blob/main/docs/hardware_support/AscendDockerUsage/build_a2_docker.md

Key details from that guide:

| Item | Value |
|------|-------|
| Base image | `swr.cn-south-1.myhuaweicloud.com/ascendhub/cann:9.0.0-910b-ubuntu22.04-py3.11` |
| ARM64 Dockerfile | `docker/ascend/Dockerfile.ascend_9.0.0_a2.arm` |
| x86 Dockerfile | `docker/ascend/Dockerfile.ascend_9.0.0_a2.x86` |
| ARM64 dependency flow | `pip install -e .[npu_aarch64]` |
| x86 dependency flow | `uv sync --locked --all-packages --extra npu --dev`, then `source /app/.venv/bin/activate` |
| Large-model option | `--shm-size=64G` |

Required container device and driver access from the guide:

```bash
--device=/dev/davinci* \
--device=/dev/davinci_manager \
--device=/dev/devmm_svm \
--device=/dev/hisi_hdc \
-v /usr/local/Ascend/driver/lib64:/usr/local/Ascend/driver/lib64:ro \
-v /usr/local/Ascend/driver/tools:/usr/local/Ascend/driver/tools:ro \
-v /usr/local/Ascend/add-ons:/usr/local/Ascend/add-ons:ro
```

On this host, do not build or pull this image until Gate 0 root-level device visibility and the traffic/disk budget are confirmed.

## Ordered Gates

### Gate 0: Driver/DCMI

Required:

```bash
npu-smi info
sudo npu-smi info
```

Expected result: a device table without DCMI initialization errors. On this host, preserve both the normal-user failure and the root result because root-only visibility is part of the operating environment.

Current status: **blocked for normal user**; root result still needs to be captured through an approved path.

### Gate 1: CANN Toolkit

Required:

```bash
test -f /usr/local/Ascend/ascend-toolkit/set_env.sh
source /usr/local/Ascend/ascend-toolkit/set_env.sh
```

Expected result: `ASCEND_HOME_PATH`, `ASCEND_TOOLKIT_HOME`, `LD_LIBRARY_PATH`, and `PYTHONPATH` include CANN paths.

Current status: **blocked** because `ascend-toolkit` and `set_env.sh` are missing.

### Gate 2: PyTorch + PTA/torch_npu Imports

Required:

```bash
python - <<'PY'
import torch
import torch_npu
print("torch", torch.__version__)
print("torch_npu", getattr(torch_npu, "__version__", "unknown"))
print("has torch.npu", hasattr(torch, "npu"))
PY
```

Expected result: imports succeed and `torch.npu` exists.

Current status: **blocked** because `torch` and `torch_npu` are missing.

### Gate 3: NPU Availability

Required:

```bash
python - <<'PY'
import torch
import torch_npu
print("available", torch.npu.is_available())
print("device_count", torch.npu.device_count())
PY
```

Expected result: `available True` and at least one visible device.

Current status: **not reached**.

### Gate 4: Tensor Smoke

Required:

```bash
python - <<'PY'
import torch
import torch_npu
x = torch.ones(4).npu()
y = (x + 1).cpu()
print(y.tolist())
PY
```

Expected result: `[2.0, 2.0, 2.0, 2.0]`.

Current status: **not reached**.

### Gate 5: VeOmni Framework Smoke

Required:

- Activate the validated CANN/PTA environment or enter the validated VeOmni A2 Docker image.
- Run the smallest VeOmni NPU smoke recipe.
- Save command, logs, framework commit, CANN/PTA versions, visible devices, and output.

Expected result: VeOmni can see NPU and run a minimal model/recipe smoke.

Current status: **not reached**.

## Install Sequence

1. Capture root-level `npu-smi info` through the platform-approved path and save it next to the normal-user DCMI failure.
2. Confirm CANN version matrix and traffic/disk budget.
3. Install CANN toolkit and kernels from HiAscend, or use the VeOmni A2/910B Docker image route.
4. Source CANN `set_env.sh` or enter the container with driver/device mounts.
5. Install a matching PyTorch and PTA/torch_npu pair.
6. Run Gates 2-4.
7. Install or activate VeOmni NPU dependencies.
8. Run Gate 5 before any MiniMax M3 work.

## What This Means For VeOmni + MiniMax M3

- NPU execution is currently **blocked**, not failed at the model layer.
- The MiniMax M3 adapter design should expose NPU capabilities such as MSA support, precision modes, long-context memory, and custom kernels through BackendAdapter/capability-matrix fields.
- Optimization and profiling should wait until correctness and NPU runtime gates pass.
- The upstream VeOmni Ascend A2 Docker guide is the preferred starting point for A2/910B containers, especially on this `aarch64` host.

## Evidence Files

- Latest inspector output: `/tmp/ascend-npu-runtime.json`
- Global skill: `$HOME/.codex/skills/ascend-npu-runtime/SKILL.md`
- Detailed playbook: `$HOME/.codex/skills/ascend-npu-runtime/references/ascend-runtime.md`
