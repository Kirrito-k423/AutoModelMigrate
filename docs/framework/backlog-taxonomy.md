# Backlog Taxonomy

## Purpose

Migration backlog items turn gap analysis into executable work. Each item should
say what is missing, which owner layer owns it, what evidence proves it, and which
lifecycle transition it blocks.

## Required Fields

| Field | Meaning | Example |
|-------|---------|---------|
| `id` | Stable item identifier. | `M3-MODEL-001` |
| `title` | Short gap or task name. | MiniMax M3 config schema is not pinned. |
| `owner_layer` | Contract responsible for the item. | `ModelSpec` |
| `severity` | Priority/risk level. | `Critical` |
| `evidence` | Source doc, command output, issue, or run log proving the gap. | `docs/cases/veomni-minimax-m3/gap-analysis.md` |
| `first_action` | Concrete next action. | Pin Hugging Face revision and capture config hash. |
| `blocks_slice` | Whether this blocks the selected first slice. | `true` |
| `dependencies` | Other backlog IDs or runtime gates required first. | `NPU-RUNTIME-001` |
| `backend_scope` | Backend or accelerator scope, if any. | `Ascend NPU`, `GPU`, `all` |
| `acceptance_gate` | Evidence that closes the item. | Config hash recorded in manifest. |
| `status_transition` | Lifecycle transition this item gates. | `Intake -> Gap Analysis` |
| `status` | Current item state. | `open`, `in_progress`, `blocked`, `closed`, `deferred` |

## Owner Layers

Use the same owner layers as the architecture contracts:

| Owner Layer | Use For |
|-------------|---------|
| `ModelSpec` | Model/algorithm semantics, architecture, operators, modalities, context, precision, assumptions. |
| `FrameworkAdapter` | Target framework hooks, registration, builders, recipes, checkpoint integration, distributed execution. |
| `BackendAdapter` | Runtime gates, accelerator capabilities, precision modes, communication, memory, graph/compile, kernels, profiler hooks. |
| `DataAdapter` | Tokenizer, processor, dataset schema, checkpoint metadata, fixtures, reference outputs. |
| `Recipe` | Reproducible commands/configs for smoke, correctness, scale, accuracy, and performance runs. |
| `ValidationSuite` | Smoke, parity, correctness, accuracy, drift, and lifecycle transition gates. |
| `OptimizationLoop` | Baseline, profiler evidence, hypotheses, metrics, regression guard, rollback. |

## Severity

| Severity | Meaning |
|----------|---------|
| `Critical` | Blocks the selected slice or can create false success. |
| `High` | Required before correctness or accuracy signoff. |
| `Medium` | Required before scale, backend support, multimodal support, or performance work. |
| `Low` | Hardening, documentation, or later automation. |

## Status

| Status | Meaning |
|--------|---------|
| `open` | Item is known and ready to plan. |
| `in_progress` | Work is underway. |
| `blocked` | Waiting on runtime, dependency, access, artifact, or user action. |
| `closed` | Acceptance gate has evidence. |
| `deferred` | Intentionally out of current phase/slice. |

## Item Template

```yaml
id: M3-BACKEND-001
title: MiniMax Sparse Attention support is unknown on Ascend NPU
owner_layer: BackendAdapter
severity: Critical
evidence:
  - docs/cases/veomni-minimax-m3/gap-analysis.md
  - docs/cases/veomni-minimax-m3/npu-runtime-evidence.md
first_action: Close NPU runtime Gates 0-5, then inspect MSA operator availability.
blocks_slice: true
dependencies:
  - NPU-RUNTIME-001
backend_scope: Ascend NPU
acceptance_gate: Capability row names unsupported/emulated/native/optimized status with evidence.
status_transition: Adapter Build -> Correctness
status: blocked
```

## MiniMax M3 Example Items

| ID | Title | owner_layer | severity | blocks_slice | backend_scope | first_action | acceptance_gate | status_transition | status |
|----|-------|-------------|----------|--------------|---------------|--------------|-----------------|-------------------|--------|
| M3-MODEL-001 | MiniMax M3 config schema is not pinned | ModelSpec | Critical | true | all | Pin Hugging Face revision and capture `config.json`, remote-code class names, dtype metadata, and license. | Manifest records revision, config hash, dtype metadata, and license evidence. | Intake -> Gap Analysis | open |
| M3-FRAMEWORK-001 | Exact VeOmni model registration path is unknown | FrameworkAdapter | Critical | true | all | Inspect VeOmni source for model builder/registry and document where MiniMax M3 plugs in. | Adapter contract names target files/symbols and construction route. | Gap Analysis -> Adapter Build | open |
| M3-FRAMEWORK-002 | Remote-code and Transformers integration policy is undecided | FrameworkAdapter | Critical | true | all | Decide whether first smoke wraps official Transformers remote code or creates a native VeOmni skeleton. | First-slice recipe records selected policy and safety caveats. | Gap Analysis -> Adapter Build | open |
| M3-BACKEND-001 | MSA backend support is unknown on GPU and NPU | BackendAdapter | Critical | true | GPU, Ascend NPU | Add capability rows for MSA and inspect native/emulated support paths. | Matrix row has maturity, evidence, blockers, and validation targets. | Adapter Build -> Correctness | open |
| M3-BACKEND-002 | Ascend NPU runtime is blocked | BackendAdapter | High | false | Ascend NPU | Capture root `npu-smi`, install/pin CANN route, install matching PyTorch/PTA and `torch_npu`. | Gates 0-5 in `docs/ops/ascend-npu-runtime.md` pass. | Adapter Build -> Correctness | blocked |
| M3-DATA-001 | Tokenizer class and special tokens are not inventoried | DataAdapter | Critical | true | all | Inspect Hugging Face tokenizer files, special tokens, chat template, and media placeholder policy. | Data contract records tokenizer class, special tokens, chat template hash, and fixture output. | Intake -> Gap Analysis | open |
| M3-DATA-002 | Checkpoint shard/index layout is not inspected | DataAdapter | Critical | true | all | Capture file list, safetensors index, shard count, dtype mix, and module name patterns. | Checkpoint evidence table exists and is linked from manifest. | Gap Analysis -> Adapter Build | open |
| M3-VALIDATION-001 | First-slice acceptance thresholds are not written | ValidationSuite | Critical | true | all | Define tiny fixture expected outputs or shape/dtype invariants. | ValidationSuite records executable tiny smoke gate. | Adapter Build -> Correctness | open |
| M3-OPT-001 | MSA performance modes are not comparable yet | OptimizationLoop | Medium | false | GPU, Ascend NPU | Record future comparison axes for dense fallback, emulated sparse, native sparse, optimized sparse. | Optimization plan has baseline, hypothesis, metrics, and rollback fields. | Correctness -> Optimize | deferred |

## Lifecycle Mapping

| Lifecycle Transition | Typical Backlog Evidence |
|----------------------|--------------------------|
| `Intake -> Gap Analysis` | Manifest draft, facts/assumptions, source/target, first slice, non-goals. |
| `Gap Analysis -> Adapter Build` | Owner-layer backlog, first actions, blocking status, dependencies. |
| `Adapter Build -> Correctness` | Adapter outputs, data fixtures, backend capability rows, smoke commands. |
| `Correctness -> Scale` | Tiny/single-device correctness evidence, declared scale target. |
| `Scale -> Optimize` | Baseline metrics and profiler-ready recipe. |
| `Optimize -> Accuracy Signoff` | Before/after metrics, regression guards, semantic checks still green. |
| `Accuracy Signoff -> Production Ready` | Accuracy/drift metrics, performance signoff, backend support evidence, docs. |

## Review Checklist

- [ ] `owner_layer` is one of the seven architecture contracts.
- [ ] `severity` is `Critical`, `High`, `Medium`, or `Low`.
- [ ] `first_action` is concrete and executable.
- [ ] `evidence` points to a source document, command output, issue, or log.
- [ ] `blocks_slice` is explicit.
- [ ] `backend_scope` is explicit when accelerator support is involved.
- [ ] `acceptance_gate` says what proves the item is closed.
- [ ] `status_transition` links the item to lifecycle progress.
