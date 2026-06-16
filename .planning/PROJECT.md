# AI Infra Migration Framework

## What This Is

AI Infra Migration Framework is a project to design a reusable engineering framework for migrating algorithms, models, datasets, training features, and inference features across AI frameworks and accelerator stacks. It targets the repeated work AI infrastructure teams do when moving support between GPU and NPU ecosystems, then validating performance and accuracy after the migration lands.

The first concrete case is to onboard MiniMax M3 through the VeOmni framework path, using it as the proving ground for the framework's interfaces, process, validation gates, and optimization loop.

## Core Value

Make cross-framework and cross-accelerator model migration repeatable, measurable, and safe enough that each new model is mostly configuration plus targeted adapters, not a bespoke rescue project.

## Requirements

### Validated

(None yet - ship to validate)

### Active

- [ ] Define a migration framework that separates model semantics, framework adapters, accelerator backends, dataset contracts, execution recipes, validation, and optimization.
- [ ] Provide an intake-to-ship workflow for model/framework migration covering research, gap analysis, adapter implementation, parity verification, performance tuning, and accuracy signoff.
- [ ] Support GPU and NPU migration paths without hard-coding either accelerator into model logic.
- [ ] Treat accuracy parity, numerical drift, throughput, memory, stability, and reproducibility as first-class artifacts.
- [ ] Use VeOmni + MiniMax M3 as the first case study and derive framework abstractions from that case instead of designing only in the abstract.
- [ ] Publish this repository under GitHub owner `Kirrito-k423` as `AutoModelMigrate`.
- [ ] Maintain an Ascend NPU runtime skill/playbook that captures CANN, PTA/torch_npu, `npu-smi`/DCMI, environment activation, and verification evidence.

### Out of Scope

- One-off manual migration scripts with no reusable framework contract - defeats the purpose of repeatability.
- A full training platform or scheduler replacement - this project should integrate with existing training/inference stacks.
- Vendor-specific kernel optimization as the first deliverable - kernel work is downstream of semantic correctness and profiling.
- Claimed support for every model family in v1 - v1 should prove one hard path well, then generalize.

## Context

AI Infra teams often repeat the same migration pattern: understand a source framework and model, map architecture and data assumptions, implement missing operators or adapters, run small correctness tests, scale to distributed training or inference, then chase performance and accuracy regressions. The work spans framework internals, distributed recipes, kernels, checkpoint formats, tokenizer/data pipelines, mixed precision, and accelerator-specific runtime behavior.

VeOmni is a useful first integration target because its public positioning emphasizes model-centric distributed recipes, modularity, any-modality training, and accelerator scaling. MiniMax M3 is a useful first model because it is recent, large, sparse-attention oriented, long-context, and multimodal, which forces the framework to handle nontrivial architecture, data, and validation concerns from day one.

## Constraints

- **Architecture**: Migration logic must not be tangled with one framework's trainer or one accelerator's runtime - otherwise future migrations will fork the framework.
- **Validation**: Every supported path needs explicit accuracy and performance gates - migration is not complete when code runs once.
- **Accelerators**: GPU and NPU should be modeled as backend capabilities with feature matrices, not as scattered conditional branches.
- **Model complexity**: MiniMax M3 likely needs sparse-attention/MSA-specific handling, long-context memory planning, and multimodal input contracts.
- **Adoption**: The framework must fit how infra teams already work: specs, manifests, adapters, reproducible recipes, profiling reports, and CI gates.

## Key Decisions

| Decision | Rationale | Outcome |
|----------|-----------|---------|
| Use a layered migration architecture: ModelSpec, FrameworkAdapter, BackendAdapter, DataAdapter, Recipe, ValidationSuite, OptimizationLoop | Keeps migration concerns independently testable and reusable across frameworks and accelerators | - Pending |
| Start with VeOmni + MiniMax M3 as the first vertical slice | A hard first case prevents a too-generic framework that fails on real sparse/long-context/multimodal requirements | - Pending |
| Define parity and performance artifacts before implementation | Prevents "it runs" from masquerading as "migration complete" | - Pending |
| Treat NPU support as a first-class backend path in the design, not a later patch | User specifically needs NPU/GPU migration coverage | - Pending |
| Publish the repository to `Kirrito-k423/AutoModelMigrate` | Keeps collaboration and future PR work anchored to the user's GitHub namespace | Published on `master`; local `origin` uses GitHub SSH-over-443 |
| Capture Ascend runtime setup as `$ascend-npu-runtime` | NPU readiness depends on driver/DCMI, CANN, PTA/torch_npu, and verification gates, not just model code | Global Codex skill created under `$HOME/.codex/skills/ascend-npu-runtime`; current host requires root for `npu-smi info` |
| Install GitHub CLI locally | Publishing to GitHub should not depend on missing system packages or sudo | `gh` 2.94.0 installed under `$HOME/.local`; repository metadata access works, git push uses SSH-over-443 deploy key |

## Evolution

This document evolves at phase transitions and milestone boundaries.

**After each phase transition** (via `$gsd-transition`):
1. Requirements invalidated? -> Move to Out of Scope with reason
2. Requirements validated? -> Move to Validated with phase reference
3. New requirements emerged? -> Add to Active
4. Decisions to log? -> Add to Key Decisions
5. "What This Is" still accurate? -> Update if drifted

**After each milestone** (via `$gsd-complete-milestone`):
1. Full review of all sections
2. Core Value check - still the right priority?
3. Audit Out of Scope - reasons still valid?
4. Update Context with current state

---
*Last updated: 2026-06-16 after publishing Phase 01 to GitHub via SSH-over-443*
