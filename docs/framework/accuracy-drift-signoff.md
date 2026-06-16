# Accuracy And Drift Signoff

## Purpose

Accuracy signoff decides whether a migrated model path preserves task-level
quality within an accepted tolerance. It is separate from correctness smoke and
parity. A tiny smoke, construction run, or parity-only run cannot by itself
prove accuracy.

This contract is owned by `ValidationSuite` and consumes validation recipe
results, baseline outputs, dataset or eval-set versions, metric definitions,
thresholds, and reviewer decisions.

## Signoff Boundaries

| Boundary | Meaning |
|----------|---------|
| Correctness | Shape, dtype, tokenizer, checkpoint, forward, loss, determinism, and runtime gates. |
| Accuracy | Task-level metric preservation against a declared baseline and eval set. |
| Numerical drift | Bounded difference in logits, loss, probabilities, generated text invariants, or metrics. |
| Performance | Throughput, latency, memory, utilization, and stability. This belongs to the optimization loop after correctness and accuracy gates are explicit. |

Accuracy signoff may require correctness evidence, but it should not be merged
with correctness evidence. Keep a separate signoff artifact so thresholds,
datasets, and reviewer approvals can be versioned.

## Required Fields

Every accuracy signoff should record:

- `signoff_id` and `version`.
- `migration_id` and manifest reference.
- Baseline implementation, version/revision, artifact hashes, dtype/backend,
  and command.
- Candidate implementation, version/revision, artifact hashes, dtype/backend,
  and command.
- Evaluation dataset or fixture set, version, license or usage notes, size, and
  sampling policy.
- Metric definitions and ownership.
- Thresholds, comparator, tolerance, blocking behavior, and lifecycle scope.
- Numerical drift policy for logits, loss, task metric, and generated outputs.
- Result summary with evidence links.
- Reviewer, date, decision, and accepted risks.

## Baseline Identity

The baseline must be stable enough to compare against. Record:

- Source artifact revision or commit.
- Config, tokenizer, processor, checkpoint index, and eval-set hashes where
  available.
- Runtime path, such as official Transformers remote code, existing framework
  implementation, or previous production baseline.
- Backend, dtype, seed policy, and deterministic settings.

If the baseline is not pinned, the signoff status must remain `pending` or
`blocked`.

## Eval Set Versioning

Evaluation data should be versioned independently from code. Record:

- Eval set id and version.
- Dataset source or fixture file.
- Modality and task scope.
- Sample count.
- Known exclusions.
- License and data-sensitivity notes.

For MiniMax M3, first-slice accuracy can remain pending while the project
starts with a tiny text correctness fixture. Image/video and long-context eval
sets should be explicit deferred gates, not omitted.

## Metric Definitions

Metrics need names, formulas, and owners. Examples:

| Metric | Use |
|--------|-----|
| Exact-match or task score | Task-level acceptance for deterministic text fixtures. |
| Logit max absolute difference | Numerical parity on controlled fixture tokens. |
| Loss relative difference | Training-mode parity where labels and masks are defined. |
| Generation invariant | Bounded behavioral check when exact text is unstable. |
| Eval benchmark score | Later task-level accuracy gate after baseline and dataset are pinned. |

## Drift Policy

Drift tolerance depends on dtype, backend, and kernel path. A signoff must name
the scope of each tolerance:

- FP32 reference tolerance.
- BF16/FP16 tolerance.
- GPU versus NPU backend tolerance.
- Dense, emulated sparse, native sparse, or optimized sparse attention path.
- Seed and deterministic rerun policy.

Do not reuse a GPU tolerance for NPU without evidence. Do not reuse a dense
attention tolerance for MiniMax Sparse Attention without parity evidence.

## Decision States

| State | Meaning | Lifecycle Behavior |
|-------|---------|--------------------|
| `pending` | Signoff has not run or baseline is incomplete. | Blocks Accuracy Signoff and Production Ready. |
| `passed` | All blocking thresholds passed and evidence is linked. | Allows declared transition. |
| `failed` | One or more blocking thresholds failed. | Blocks transition until fixed or accepted as risk. |
| `blocked` | Prerequisite missing, such as baseline, eval set, runtime, or artifact pin. | Blocks scoped transition. |
| `accepted_risk` | Reviewer accepts a failed or incomplete result for a declared scope. | Allows transition only for the accepted scope and must be visible in release notes. |

## MiniMax M3 Guidance

For the first VeOmni + MiniMax M3 slice:

- Accuracy signoff is expected to remain pending until a baseline path and eval
  set are pinned.
- Tiny text smoke can provide correctness evidence but not model-quality
  signoff.
- Multimodal and 1M-context signoff should be deferred with explicit blockers.
- Ascend NPU signoff is blocked until root `npu-smi`, CANN, `torch_npu`, tensor
  smoke, VeOmni smoke, and operator evidence exist.
- MSA paths should distinguish dense fallback, emulated sparse, native sparse,
  and optimized sparse results.

## Review Checklist

- [ ] Baseline and candidate revisions are pinned.
- [ ] Eval set and fixture versions are pinned.
- [ ] Metrics and thresholds have owners.
- [ ] Drift tolerance names dtype, backend, and kernel path scope.
- [ ] Blocking thresholds map to lifecycle transitions.
- [ ] Reviewer and evidence links are recorded.
- [ ] Tiny smoke or parity-only evidence is not treated as accuracy signoff.
