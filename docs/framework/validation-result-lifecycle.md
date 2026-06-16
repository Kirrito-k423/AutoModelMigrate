# Validation Result Lifecycle

## Purpose

Validation results decide whether a migration may advance through lifecycle
states. They are not passive reports. A failed or blocked required gate must
block the transition it protects unless a reviewer records an accepted risk for
a narrow scope.

## Result States

| Result | Meaning | Default Transition Behavior |
|--------|---------|-----------------------------|
| `pending` | Gate or signoff has not run. | Blocks required transitions. |
| `passed` | Evidence meets criteria. | Allows the scoped transition. |
| `failed` | Evidence violates criteria. | Blocks the scoped transition. |
| `blocked` | Prerequisite is missing. | Blocks the scoped transition but may allow unrelated work. |
| `skipped` | Out of declared scope. | Allows transition only when non-goal and reviewer rationale are recorded. |
| `accepted_risk` | Reviewer accepts a failed or incomplete result. | Allows only the accepted scope and must remain visible. |

## Transition Mapping

| Lifecycle Transition | Required Validation Evidence |
|----------------------|------------------------------|
| Intake -> Gap Analysis | Manifest/schema gate and evidence inventory exist. |
| Gap Analysis -> Adapter Build | Model config, artifact, data, backend, and framework gaps are explicit. |
| Adapter Build -> Correctness | Tokenizer/processor fixture, checkpoint metadata, and construction smoke gates are ready or scoped. |
| Correctness -> Scale | Forward/logit parity, loss parity where applicable, determinism, and non-NPU backend gates pass. |
| Scale -> Optimize | Correctness remains green at declared scale, and performance baseline recipe exists. |
| Optimize -> Accuracy Signoff | Optimized path does not regress correctness gates and has before/after evidence. |
| Accuracy Signoff -> Production Ready | Accuracy/drift signoff passes for declared tasks, backends, dtypes, and accepted risk scope. |

## `blocks_transition`

Each validation recipe result and accuracy signoff result should include
`blocks_transition`.

Rules:

1. `failed` plus `blocking: true` sets `blocks_transition: true`.
2. `blocked` plus `blocking: true` sets `blocks_transition: true` for the
   blocked scope.
3. `pending` sets `blocks_transition: true` when the gate is required for the
   requested lifecycle transition.
4. `skipped` sets `blocks_transition: false` only when non-goal rationale is
   recorded.
5. `accepted_risk` sets `blocks_transition: false` only for the accepted scope.

## Backend-Scoped Blocking

Backend-specific blockers should block backend support claims without freezing
unrelated reference-path validation.

Example: Ascend NPU can remain blocked on root `npu-smi`, CANN, `torch_npu`,
tensor smoke, and VeOmni smoke while a CPU/GPU/reference correctness recipe
continues. The migration cannot claim Ascend NPU support until those backend
runtime gates pass.

## Backlog Integration

When a validation result blocks a transition, create or update a backlog item
with:

- `owner_layer`
- `severity`
- `evidence`
- `first_action`
- `blocks_slice`
- `backend_scope`
- `acceptance_gate`
- `lifecycle_transition`

This ties `ValidationSuite` failures directly to the backlog taxonomy instead
of leaving them as unowned report notes.

## MiniMax M3 Example

For VeOmni + MiniMax M3:

- Pending tokenizer/processor fixture blocks Adapter Build -> Correctness.
- Pending checkpoint metadata blocks Adapter Build -> Correctness.
- Pending forward/logit parity blocks Correctness -> Scale.
- Blocked Ascend NPU runtime blocks NPU support claims, but not reference path
  artifact inspection.
- Pending accuracy/drift signoff blocks Accuracy Signoff -> Production Ready.
- MSA native or optimized status cannot be claimed until sparse attention
  correctness and performance evidence exists.

## Review Checklist

- [ ] Every required gate has a result state.
- [ ] Every failed, blocked, or pending required gate states the transition it blocks.
- [ ] Backend-blocked results are scoped to that backend.
- [ ] Accepted risks are explicit and narrow.
- [ ] Blocking results create or update backlog items.
