---
phase: 03
name: correctness-and-accuracy-harness
created: 2026-06-16
status: ready-for-planning
source:
  - .planning/ROADMAP.md
  - .planning/phases/aimf-02-core-migration-architecture/02-CONTEXT.md
  - docs/framework/adapter-contracts.md
  - docs/framework/migration-manifest-spec.md
  - docs/framework/migration-lifecycle.md
  - docs/framework/backlog-taxonomy.md
  - docs/cases/veomni-minimax-m3/gap-analysis.md
---

# Phase 03 Context: Correctness And Accuracy Harness

## Phase Goal

Define validation recipes that prove semantic migration correctness before optimization.

## Success Criteria

1. Smoke, unit, parity, and task-level evaluation recipes are specified.
2. Accuracy and numerical drift thresholds are represented as versioned artifacts.
3. Validation results can block migration status transitions.

## Inputs From Earlier Phases

### Phase 1 Case Evidence

- The first concrete case remains MiniMax M3 through VeOmni.
- First slice is "MiniMax M3 reference artifact intake to VeOmni tiny text smoke."
- MiniMax M3 risks that validation must not hide: MSA/sparse attention, 1M advertised context, native text/image/video support, tokenizer/processor unknowns, checkpoint shard/index unknowns, precision policy, and NPU runtime blockers.
- Ascend NPU is not ready: normal-user `npu-smi info` fails, root-approved device evidence is still required, CANN is missing, and `torch_npu` is missing.

### Phase 2 Architecture Contracts

- `ValidationSuite` owns smoke, unit, parity, correctness, accuracy, drift, runtime, performance readiness, and status transition gates.
- `Recipe` owns reproducible command/config surfaces and must record backend, dtype, batch, sequence/context length, hardware, and environment.
- `DataAdapter` owns tokenizer, processor, dataset schema, checkpoint metadata, fixtures, and reference outputs.
- `BackendAdapter` owns runtime gates and backend capability evidence; NPU status can remain blocked while reference/GPU validation work continues.
- Lifecycle transitions are evidence-gated: Correctness must pass before Scale or Optimize, and Accuracy Signoff must require declared metric/drift evidence.

## Working Decisions

- **D-03-01:** Correctness is layered: schema/config inspection, artifact inventory, fixture generation, smoke construction, tokenizer/processor parity, checkpoint/load mapping, forward/logit parity, loss parity where applicable, and deterministic repeatability.
- **D-03-02:** Accuracy is a separate signoff from correctness. A tiny smoke can prove wiring, but it cannot prove model quality.
- **D-03-03:** Validation recipes must be manifest-addressable so a migration can declare which gates apply to a given source/target/backend path.
- **D-03-04:** Backend status can be `blocked-runtime` without blocking non-NPU reference work, but any NPU support claim must require root `npu-smi`, CANN, `torch_npu`, tensor smoke, and VeOmni smoke evidence first.
- **D-03-05:** MiniMax M3 validation starts text-only and tiny by default, but the harness must preserve deferred image/video and long-context gates instead of erasing them.
- **D-03-06:** Drift thresholds must be versioned artifacts tied to baseline implementation, model revision, dtype, hardware/backend, seed policy, metric definition, and accepted tolerance.
- **D-03-07:** Validation results must be allowed to block lifecycle transitions and backlog acceptance gates, not just appear as optional reports.

## Phase 3 Deliverables

Expected framework docs:

- Correctness validation harness specification.
- Validation recipe schema or template covering smoke, unit, parity, task-level, runtime, and transition gates.
- MiniMax M3 first-slice validation example grounded in VeOmni tiny text smoke.
- Accuracy and drift signoff artifact specification.
- Threshold/versioning guidance for metric baselines, numerical drift, and acceptance criteria.
- Lifecycle integration showing how validation results block transitions.

## Validation Surfaces To Cover

| Surface | Owner Layer | Minimum Evidence |
|---------|-------------|------------------|
| Manifest/schema gate | ValidationSuite | Manifest parses and required contract sections exist. |
| Model config gate | ModelSpec / DataAdapter | Revision, config hash, architecture fields, dtype metadata, and context limits recorded. |
| Tokenizer/processor gate | DataAdapter | Class, special tokens, chat template, media placeholders, fixture input/output fields. |
| Checkpoint gate | DataAdapter | File list, index metadata, shard count, dtype mix, module-name mapping. |
| Tiny construction smoke | FrameworkAdapter / Recipe | Command, config, backend, dtype, shape, and logs. |
| Forward/logit parity | ValidationSuite | Baseline, candidate, fixture, tolerance, seed policy, and output comparison. |
| Loss parity | ValidationSuite | Training-mode fixture, labels/mask policy, tolerance, and deterministic rerun. |
| Task-level accuracy | ValidationSuite | Dataset/eval set, metric, baseline score, candidate score, drift threshold. |
| Backend runtime gate | BackendAdapter | Device/runtime setup logs, tensor smoke, framework smoke. |
| Transition gate | ValidationSuite / lifecycle | Pass/fail result blocks or allows lifecycle movement. |

## MiniMax M3 First-Slice Guidance

The first MiniMax M3 validation slice should not require a full 428B-class load, full 1M context, multimodal forward, NPU execution, or optimization. It should define enough validation structure to make the first implementation safe:

1. Pin source revision and inspect config/tokenizer/processor/checkpoint metadata.
2. Create a tiny text fixture with expected tokenizer/processor output.
3. Decide reference path, likely official Transformers remote code if safe and feasible.
4. Define construction or forward smoke invariants: shapes, dtypes, output keys, and explicit unsupported MSA/NPU status.
5. Record parity thresholds and blockers before any VeOmni support claim.

## Open Questions

None blocking planning. Implementation phases may need to inspect MiniMax M3 and VeOmni source before filling exact fixture values, but Phase 3 can define the reusable validation contracts now.
