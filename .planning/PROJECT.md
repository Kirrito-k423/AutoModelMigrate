# AI Infra Migration Framework

## What This Is

AI Infra Migration Framework is a reusable engineering framework for planning,
tracking, validating, and handing off AI model/framework/accelerator migrations.
It targets the repeated work AI infrastructure teams do when moving algorithms,
models, datasets, training features, and inference features across framework and
accelerator stacks, then proving correctness, accuracy, and performance before
support claims are made.

The first concrete case is VeOmni + MiniMax M3. It is now packaged as a
reference case and template source for future migrations, not as a runtime
support claim.

## Core Value

Make cross-framework and cross-accelerator model migration repeatable, measurable, and safe enough that each new model is mostly configuration plus targeted adapters, not a bespoke rescue project.

## Requirements

### Validated

- [x] Define a migration framework that separates model semantics, framework adapters, accelerator backends, dataset contracts, execution recipes, validation, and optimization. - v1.0
- [x] Provide an intake-to-ship workflow for model/framework migration covering research, gap analysis, adapter implementation, parity verification, performance tuning, and accuracy signoff. - v1.0
- [x] Support GPU and NPU migration paths without hard-coding either accelerator into model logic. - v1.0
- [x] Treat accuracy parity, numerical drift, throughput, memory, stability, and reproducibility as first-class artifacts. - v1.0
- [x] Use VeOmni + MiniMax M3 as the first case study and derive framework abstractions from that case instead of designing only in the abstract. - v1.0
- [x] Publish this repository under GitHub owner `Kirrito-k423` as `AutoModelMigrate`. - v1.0
- [x] Maintain an Ascend NPU runtime skill/playbook that captures CANN, PTA/torch_npu, `npu-smi`/DCMI, environment activation, and verification evidence. - v1.0

### Active

- [ ] Add migration templates for at least two more model families after MiniMax M3.
- [ ] Add integration surfaces for additional training/inference frameworks.
- [ ] Add dashboard/reporting surfaces for migration portfolio status.
- [ ] Close runtime execution evidence for a scoped VeOmni + MiniMax M3 smoke path only after backend, validation, accuracy, and performance gates pass.

### Out of Scope

- One-off manual migration scripts with no reusable framework contract - defeats the purpose of repeatability.
- A full training platform or scheduler replacement - this project should integrate with existing training/inference stacks.
- Vendor-specific kernel optimization as the first deliverable - kernel work is downstream of semantic correctness and profiling.
- Claimed support for every model family in v1 - v1 should prove one hard path well, then generalize.

## Context

AI Infra teams often repeat the same migration pattern: understand a source framework and model, map architecture and data assumptions, implement missing operators or adapters, run small correctness tests, scale to distributed training or inference, then chase performance and accuracy regressions. The work spans framework internals, distributed recipes, kernels, checkpoint formats, tokenizer/data pipelines, mixed precision, and accelerator-specific runtime behavior.

VeOmni is a useful first integration target because its public positioning emphasizes model-centric distributed recipes, modularity, any-modality training, and accelerator scaling. MiniMax M3 is a useful first model because it is recent, large, sparse-attention oriented, long-context, and multimodal, which forces the framework to handle nontrivial architecture, data, and validation concerns from day one.

## Current State

v1.0 shipped on 2026-06-17.

The repository now contains:

- A MiniMax M3 + VeOmni intake, assumption map, gap analysis, runtime evidence,
  and reference-case index under `docs/cases/veomni-minimax-m3/`.
- Reusable migration contracts under `docs/framework/`, including manifest,
  adapter, backend capability, lifecycle, backlog, correctness, accuracy,
  performance, optimization, template-pack, next-case, and handoff artifacts.
- Phase verification, UAT, security, Nyquist validation, and milestone audit
  evidence under `.planning/`.
- Archived v1.0 roadmap, requirements, and milestone audit under
  `.planning/milestones/`.

Known open reality: v1.0 is a framework and evidence package. It does not claim
MiniMax M3 runs in VeOmni, that accuracy has been measured, or that Ascend NPU
execution is ready. Those remain future evidence-gated implementation goals.

## Next Milestone Goals

- Apply the v1.0 templates to at least one additional model/framework migration
  to test generality beyond MiniMax M3.
- Decide the next concrete framework integration surface and identify the first
  executable smoke slice.
- Close or explicitly defer the Ascend NPU runtime gates before any NPU
  execution support claim.
- Add portfolio/reporting views only after there are multiple migrations worth
  comparing.

## Constraints

- **Architecture**: Migration logic must not be tangled with one framework's trainer or one accelerator's runtime - otherwise future migrations will fork the framework.
- **Validation**: Every supported path needs explicit accuracy and performance gates - migration is not complete when code runs once.
- **Accelerators**: GPU and NPU should be modeled as backend capabilities with feature matrices, not as scattered conditional branches.
- **Model complexity**: MiniMax M3 likely needs sparse-attention/MSA-specific handling, long-context memory planning, and multimodal input contracts.
- **Adoption**: The framework must fit how infra teams already work: specs, manifests, adapters, reproducible recipes, profiling reports, and CI gates.

## Key Decisions

| Decision | Rationale | Outcome |
|----------|-----------|---------|
| Use a layered migration architecture: ModelSpec, FrameworkAdapter, BackendAdapter, DataAdapter, Recipe, ValidationSuite, OptimizationLoop | Keeps migration concerns independently testable and reusable across frameworks and accelerators | Validated in v1.0 framework contracts |
| Start with VeOmni + MiniMax M3 as the first vertical slice | A hard first case prevents a too-generic framework that fails on real sparse/long-context/multimodal requirements | Packaged as v1.0 reference case |
| Define parity and performance artifacts before implementation | Prevents "it runs" from masquerading as "migration complete" | Validated through Phase 3 and Phase 4 contracts |
| Treat NPU support as a first-class backend path in the design, not a later patch | User specifically needs NPU/GPU migration coverage | Validated as capability descriptors and runtime blockers |
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
*Last updated: 2026-06-17 after v1.0 milestone closeout*
