# Correctness Validation Harness

## Purpose

The correctness validation harness defines how a migration proves semantic
correctness before scale, optimization, or production claims. A successful
smoke run is only one gate. It does not prove tokenizer parity, checkpoint
mapping, forward parity, loss parity, accuracy, or backend readiness by itself.

This harness is owned by `ValidationSuite` and consumes evidence from
`ModelSpec`, `DataAdapter`, `FrameworkAdapter`, `BackendAdapter`, and `Recipe`.

## Owner Layers

| Layer | Responsibility In Validation |
|-------|------------------------------|
| `ModelSpec` | Declares model architecture, operators, modality, context limits, dtype policy, and assumptions to validate. |
| `DataAdapter` | Provides tokenizer, processor, checkpoint metadata, fixtures, and reference outputs. |
| `FrameworkAdapter` | Provides target framework construction, model/config builders, and execution hooks. |
| `BackendAdapter` | Provides runtime gates, precision support, device visibility, operator availability, and backend blockers. |
| `Recipe` | Provides reproducible commands, configs, backend, dtype, hardware, seed, and environment. |
| `ValidationSuite` | Owns gate ordering, pass/fail/blocked semantics, evidence requirements, and transition decisions. |

## Gate Order

Run the smallest meaningful gates first. Later gates may depend on earlier
evidence, but blocked backend gates do not need to stop reference-path work.

| Gate | Owner | Required Evidence | Blocks Transition |
|------|-------|-------------------|-------------------|
| Manifest/schema gate | `ValidationSuite` | Manifest parses, required sections exist, owner layers and evidence refs are present. | Intake -> Gap Analysis |
| Model config gate | `ModelSpec` / `DataAdapter` | Source revision, config hash, architecture fields, dtype metadata, context limits. | Gap Analysis -> Adapter Build |
| Tokenizer/processor fixture gate | `DataAdapter` | Tokenizer class, special tokens, chat template, media placeholders, fixture inputs/outputs. | Adapter Build -> Correctness |
| Checkpoint metadata gate | `DataAdapter` | File list, shard/index metadata, dtype mix, module-name mapping, load non-goals. | Adapter Build -> Correctness |
| Construction smoke gate | `FrameworkAdapter` / `Recipe` | Command, config, target framework logs, shape/dtype/output-key invariant. | Adapter Build -> Correctness |
| Forward/logit parity gate | `ValidationSuite` | Baseline output, candidate output, fixture, tolerance, dtype/backend, seed policy. | Correctness -> Scale |
| Loss parity gate | `ValidationSuite` | Training-mode fixture, labels, loss mask policy, tolerance, deterministic rerun. | Correctness -> Scale when training is in scope |
| Deterministic repeatability gate | `ValidationSuite` | Repeated run with declared seeds and expected stable fields. | Correctness -> Scale |
| Backend runtime gate | `BackendAdapter` | Device/runtime setup logs, tensor smoke, framework smoke, operator support evidence. | Backend-specific support claims |
| Transition gate | `ValidationSuite` / lifecycle | Aggregated result, blockers, accepted risk, reviewer, evidence bundle. | Any lifecycle transition |

## Gate Semantics

| Status | Meaning | Transition Behavior |
|--------|---------|---------------------|
| `pending` | Gate has not run yet. | Blocks transitions that require the gate. |
| `passed` | Evidence satisfies the declared acceptance criteria. | Allows dependent transitions. |
| `failed` | Gate ran and violated acceptance criteria. | Blocks dependent transitions until fixed or accepted as risk. |
| `blocked` | Gate cannot run because a prerequisite is missing. | Blocks support claims for the blocked scope, but may allow unrelated reference work. |
| `skipped` | Gate is out of declared scope. | Allowed only when non-goal and reviewer rationale are recorded. |

Use `blocked` for missing runtime prerequisites such as CANN or `torch_npu`.
Do not label a missing runtime as a failed model correctness gate.

## Evidence Requirements

Every gate result should record:

- Gate id and type.
- Owner layer.
- Migration id and manifest reference.
- Baseline and candidate reference.
- Fixture id and fixture artifact path.
- Command or manual procedure.
- Backend, dtype, hardware, and environment.
- Seed and determinism policy.
- Expected evidence and actual evidence.
- Blocking lifecycle transition.
- Reviewer or automated checker.

## MiniMax M3 First Slice

The first MiniMax M3 validation slice should be text-only and tiny. It should
avoid full 428B-class checkpoint load, full 1M context, image/video forward,
NPU execution, and optimization.

Minimum gates:

1. Pin MiniMax M3 revision and record config/tokenizer/checkpoint metadata.
2. Define one tiny text fixture and record tokenizer/processor expected fields.
3. Choose a safe reference path, such as official Transformers remote code if
   project policy allows it.
4. Define a VeOmni construction or forward smoke invariant: output keys, shape,
   dtype, and explicit unsupported features.
5. Add forward/logit parity as pending until the reference and candidate paths
   are both executable.
6. Keep Ascend NPU gates blocked until root `npu-smi`, CANN, `torch_npu`,
   tensor smoke, and VeOmni smoke evidence exist.

MSA, multimodal, long-context, and NPU gates must remain visible even when the
first slice is text-only.

## Backend-Blocked Behavior

Backend readiness is scoped. A blocked Ascend NPU gate does not block a CPU/GPU
or reference-framework correctness gate unless the migration explicitly claims
NPU support for that result.

For Ascend NPU support claims, require:

- Root-approved `npu-smi info` evidence.
- CANN toolkit and environment activation evidence.
- Matching PyTorch/PTA or `torch_npu` import evidence.
- Tiny NPU tensor smoke.
- Smallest VeOmni NPU smoke.
- Operator-specific evidence for MSA or any emulated fallback.

## Review Checklist

- [ ] The validation recipe names the migration manifest and model revision.
- [ ] Each gate has owner layer, command/procedure, expected evidence, and status.
- [ ] Smoke, parity, loss, accuracy, runtime, and transition gates are not conflated.
- [ ] Blocked runtime gates do not masquerade as passed or failed correctness.
- [ ] MiniMax M3 deferred gates for MSA, multimodal, long context, and NPU remain visible.
- [ ] Correctness gates pass before Scale or Optimize.
