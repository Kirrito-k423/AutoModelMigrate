<!-- GSD:project-start source:PROJECT.md -->

## Project

**AI Infra Migration Framework**

AI Infra Migration Framework is a project to design a reusable engineering framework for migrating algorithms, models, datasets, training features, and inference features across AI frameworks and accelerator stacks. It targets the repeated work AI infrastructure teams do when moving support between GPU and NPU ecosystems, then validating performance and accuracy after the migration lands.

The first concrete case is to onboard MiniMax M3 through the VeOmni framework path, using it as the proving ground for the framework's interfaces, process, validation gates, and optimization loop.

**Core Value:** Make cross-framework and cross-accelerator model migration repeatable, measurable, and safe enough that each new model is mostly configuration plus targeted adapters, not a bespoke rescue project.

### Constraints

- **Architecture**: Migration logic must not be tangled with one framework's trainer or one accelerator's runtime - otherwise future migrations will fork the framework.
- **Validation**: Every supported path needs explicit accuracy and performance gates - migration is not complete when code runs once.
- **Accelerators**: GPU and NPU should be modeled as backend capabilities with feature matrices, not as scattered conditional branches.
- **Model complexity**: MiniMax M3 likely needs sparse-attention/MSA-specific handling, long-context memory planning, and multimodal input contracts.
- **Adoption**: The framework must fit how infra teams already work: specs, manifests, adapters, reproducible recipes, profiling reports, and CI gates.

<!-- GSD:project-end -->

<!-- GSD:stack-start source:research/STACK.md -->

## Technology Stack

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

<!-- GSD:stack-end -->

<!-- GSD:conventions-start source:CONVENTIONS.md -->

## Conventions

Conventions not yet established. Will populate as patterns emerge during development.
<!-- GSD:conventions-end -->

<!-- GSD:architecture-start source:ARCHITECTURE.md -->

## Architecture

Architecture not yet mapped. Follow existing patterns found in the codebase.
<!-- GSD:architecture-end -->

<!-- GSD:skills-start source:skills/ -->

## Project Skills

No project skills found. Add skills to any of: `.claude/skills/`, `.agents/skills/`, `.cursor/skills/`, `.github/skills/`, or `.codex/skills/` with a `SKILL.md` index file.
<!-- GSD:skills-end -->

<!-- GSD:workflow-start source:GSD defaults -->

## GSD Workflow Enforcement

Before using Edit, Write, or other file-changing tools, start work through a GSD command so planning artifacts and execution context stay in sync.

Use these entry points:

- `/gsd-quick` for small fixes, doc updates, and ad-hoc tasks
- `/gsd-debug` for investigation and bug fixing
- `/gsd-execute-phase` for planned phase work

Do not make direct repo edits outside a GSD workflow unless the user explicitly asks to bypass it.
<!-- GSD:workflow-end -->

<!-- GSD:profile-start -->

## Developer Profile

> Profile not yet configured. Run `/gsd-profile-user` to generate your developer profile.
> This section is managed by `generate-claude-profile` -- do not edit manually.
<!-- GSD:profile-end -->
