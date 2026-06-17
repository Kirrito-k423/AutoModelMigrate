# VeOmni + MiniMax M3 Reference Case

## Purpose

This reference case packages the VeOmni + MiniMax M3 migration evidence as a
worked example for the AI Infra Migration Framework. It is case evidence and a
generic contract example where noted; it is not a support claim that MiniMax M3
runs in VeOmni or on Ascend NPU.

## Current Support Status

| Area | support status | Evidence |
|------|----------------|----------|
| MiniMax M3 source facts | Intake and assumptions captured; artifact revision and hashes still pending. | `docs/cases/veomni-minimax-m3/intake.md`, `docs/cases/veomni-minimax-m3/assumptions.md` |
| VeOmni adapter path | Gap analysis captured; exact model registration and remote-code policy still pending. | `docs/cases/veomni-minimax-m3/gap-analysis.md` |
| Reference validation | Validation recipe example exists; execution evidence remains pending or blocked by missing artifact/adapter work. | `docs/framework/examples/veomni-minimax-m3.validation-recipe.yaml` |
| Accuracy | Signoff example exists; no measured accuracy result or approved drift decision yet. | `docs/framework/examples/veomni-minimax-m3.accuracy-signoff.yaml` |
| Performance | Profile and optimization examples exist for the tiny text documentation pattern; support claims still require runtime evidence. | `docs/framework/examples/veomni-minimax-m3.performance-profile.yaml`, `docs/framework/examples/veomni-minimax-m3.optimization-report.md` |
| Ascend NPU | blocked before framework smoke; current host lacks captured root device evidence, CANN, and torch_npu. | `docs/cases/veomni-minimax-m3/npu-runtime-evidence.md`, `docs/ops/ascend-npu-runtime.md` |

## Case Artifact Index

| Artifact | Kind | Purpose | reusable lesson | support status |
|----------|------|---------|-----------------|----------------|
| `docs/cases/veomni-minimax-m3/intake.md` | case evidence | Captures source, target, model dimensions, first slice, validation gates, and non-goals. | Intake should separate facts, assumptions, backend scope, and first slice before code. | Intake evidence only; not a support claim. |
| `docs/cases/veomni-minimax-m3/assumptions.md` | case evidence | Tracks public facts and assumptions for MiniMax M3, VeOmni, MSA, 1M context, multimodality, checkpoint, tokenizer, and Ascend NPU. | New model migrations need explicit fact/assumption tables with validation actions. | Assumptions remain open until pinned evidence exists. |
| `docs/cases/veomni-minimax-m3/gap-analysis.md` | case evidence | Converts unknowns into ModelSpec, FrameworkAdapter, BackendAdapter, DataAdapter, ValidationSuite, and OptimizationLoop gaps. | Owner-layer gaps make blockers plannable and keep runtime blockers out of model correctness. | Planning evidence only; adapter execution not claimed. |
| `docs/cases/veomni-minimax-m3/npu-runtime-evidence.md` | case evidence | Records Ascend NPU runtime gates and current host blockers. | Backend readiness can be blocked while reference/model/framework work continues. | Ascend NPU is blocked. |
| `docs/cases/veomni-minimax-m3/reusable-deltas.md` | case evidence | Explains which case lessons were promoted into reusable templates. | Hard case facts should become generic fields, not generic defaults. | Documentation evidence. |

## Framework Example Index

| Example | Kind | Purpose | reusable lesson | support status |
|---------|------|---------|-----------------|----------------|
| `docs/framework/examples/veomni-minimax-m3.manifest.yaml` | generic contract example backed by case evidence | Shows how the manifest can represent MiniMax M3 source/target scope, adapters, backend matrix, validation, optimization, and evidence. | The manifest should make unknowns and blockers machine-checkable. | Example only; unsupported fields remain explicit. |
| `docs/framework/examples/veomni-minimax-m3-capabilities.md` | generic contract example backed by case evidence | Shows GPU and Ascend NPU capability rows with maturity, blockers, and next actions. | Backend capability rows prevent scattered accelerator-specific branches. | NPU rows are blocked or unsupported; no NPU claim. |
| `docs/framework/examples/veomni-minimax-m3.validation-recipe.yaml` | generic contract example backed by case evidence | Shows first-slice validation gates and blocked runtime handling. | Validation should distinguish failed correctness from blocked runtime. | Recipe example only; most gates remain pending or blocked. |
| `docs/framework/examples/veomni-minimax-m3.accuracy-signoff.yaml` | generic contract example backed by case evidence | Shows pending baseline, threshold, drift policy, lifecycle decision, and review fields. | Accuracy signoff needs baseline and threshold before approval. | Pending; no accuracy support claim. |
| `docs/framework/examples/veomni-minimax-m3.performance-profile.yaml` | generic contract example backed by case evidence | Shows workload identity, backend metrics, profiler artifacts, compile overhead, runtime stability, and regression guard. | Performance evidence must be tied to a declared slice and backend. | Example profile only; support claims require reproducible evidence. |
| `docs/framework/examples/veomni-minimax-m3.optimization-report.md` | generic contract example backed by case evidence | Shows a lab-notebook style before/after optimization report. | Optimization needs correctness regression checks and rollback criteria. | Example only; Ascend NPU remains blocked. |

## Current Blockers

| Blocker | Owner Layer | Impact |
|---------|-------------|--------|
| Pinned artifact inspection is missing. | ModelSpec, DataAdapter | Config, tokenizer, processor, checkpoint index, dtype metadata, and license hashes are not final. |
| Adapter implementation path is unknown. | FrameworkAdapter | VeOmni model registration, tokenizer/processor builder, and remote-code policy need source inspection. |
| Validation execution is pending. | ValidationSuite | Tiny fixture outputs, parity commands, and expected invariants are not executed. |
| Accuracy measurement is pending. | ValidationSuite | Baseline, evaluation set, threshold, drift result, and reviewer decision are not complete. |
| Performance evidence is example-scoped. | OptimizationLoop | Reproducible performance support requires declared hardware, backend, workload, profiler artifacts, and regression guard. |
| Ascend NPU runtime gates are blocked. | BackendAdapter | Root-approved device evidence, CANN, torch_npu, tensor smoke, VeOmni smoke, and MiniMax M3 smoke are not complete. |

## Boundary Rules

- Case evidence can mention MiniMax M3, VeOmni, Ascend NPU, MSA, 1M context,
  multimodal fixtures, and current-host blockers.
- Generic contracts should stay in `docs/framework/` and remain model-neutral.
- A blocked backend runtime is not a model correctness failure.
- A worked example is not a support claim until validation, accuracy,
  performance, backend capability, and runtime evidence are captured for the
  claimed scope.
