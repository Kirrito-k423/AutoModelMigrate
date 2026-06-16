# Stack Research

**Domain:** AI infrastructure migration framework for model/framework/backend portability
**Researched:** 2026-06-16
**Confidence:** MEDIUM

## Recommended Stack

### Core Technologies

| Technology | Version | Purpose | Why Recommended |
|------------|---------|---------|-----------------|
| Python | 3.10+ | Framework implementation, adapters, validation runners | Matches AI training/inference ecosystems and VeOmni-style workflows. |
| PyTorch | Project-pinned | Reference tensor/model semantics and GPU execution baseline | Most open model implementations, checkpoints, and validation fixtures use PyTorch semantics. |
| VeOmni | Source-pinned | First target framework for distributed recipe integration | VeOmni is built for single- and multi-modal pre/post-training with modular, model-centric distributed recipes. |
| YAML/JSON Schema | Draft 2020-12 or project-pinned | Migration manifest, capability matrix, validation reports | Gives migrations machine-checkable contracts before code is written. |
| Markdown specs | GSD `.planning` + `docs/` | Human-readable design, signoff, and evidence records | AI Infra migration requires reviewable decisions, not just scripts. |

### Supporting Libraries

| Library | Version | Purpose | When to Use |
|---------|---------|---------|-------------|
| pytest | Project-pinned | Unit, fixture, and parity tests | Shape/dtype/checkpoint/forward/loss parity gates. |
| pydantic or dataclasses-json | Project-pinned | Typed manifest parsing | Keep migration manifests strict but ergonomic. |
| safetensors / transformers utilities | Project-pinned | Checkpoint/tokenizer inspection | Only if MiniMax M3 artifacts expose compatible files. |
| torch.profiler / vendor profiler hooks | Backend-pinned | Performance evidence | Required after correctness gates pass. |

### Development Tools

| Tool | Purpose | Notes |
|------|---------|-------|
| GSD Core | Spec-driven workflow | Project is initialized with `$gsd-new-project`; next step is `$gsd-plan-phase 1`. |
| rg | Fast source search | Required for grounding adapter and symbol plans. |
| Git | Trace planning and implementation | GSD commit flow expects a repo; this workspace should be initialized. |

## Alternatives Considered

| Recommended | Alternative | When to Use Alternative |
|-------------|-------------|-------------------------|
| Manifest + adapters | Ad hoc migration scripts | Only for one-off experiments that will never be reused. |
| Capability matrix | Backend-specific branches in model code | Backend branches are acceptable only behind a BackendAdapter boundary. |
| VeOmni first case | Generic framework first | Generic-first design is risky because MiniMax M3 stresses sparse attention, long context, multimodality, and backend performance at once. |

## What NOT to Use

| Avoid | Why | Use Instead |
|-------|-----|-------------|
| "Runs once" as migration signoff | It misses silent tokenizer, checkpoint, attention-mask, precision, and accuracy regressions | ValidationSuite with explicit evidence. |
| Model forks per accelerator | GPU/NPU divergence becomes unmaintainable | Backend capability descriptors and optimization reports. |
| Kernel optimization before parity | Performance changes can hide semantic errors | Correctness gates first, then profiling. |

## Sources

- VeOmni GitHub: https://github.com/ByteDance-Seed/VeOmni
- VeOmni docs: https://veomni.readthedocs.io/en/latest/
- MiniMax M3 GitHub: https://github.com/MiniMax-AI/MiniMax-M3
- MiniMax M3 Hugging Face: https://huggingface.co/MiniMaxAI/MiniMax-M3
- MiniMax Sparse Attention paper: https://arxiv.org/html/2606.13392v1

---
*Stack research for: AI Infra Migration Framework*
*Researched: 2026-06-16*
