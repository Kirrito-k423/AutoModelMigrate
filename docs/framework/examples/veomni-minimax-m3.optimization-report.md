# VeOmni + MiniMax M3 Optimization Report

## Phase 3 Tiny Text Slice

This example records a single optimization attempt for the Phase 3 tiny text
slice. It is written like a lab notebook so reviewers can trace the baseline,
the change, the evidence, and the rollback path without guessing.

## Identity

| Field | Value |
|-------|-------|
| Migration | `veomni-minimax-m3` |
| Slice | Phase 3 tiny text path |
| Baseline ID | `minimax-m3-reference-tiny-text` |
| Candidate ID | `veomni-minimax-m3-tiny-text` |
| Backend | GPU |
| Dtype | BF16 |
| Hardware | developer workstation GPU |
| Framework | VeOmni |
| Recipe | `docs/framework/examples/veomni-minimax-m3.validation-recipe.yaml` |

## Baseline

The baseline performance profile is
`docs/framework/examples/veomni-minimax-m3.performance-profile.yaml`.

Baseline facts:

- Correctness for the tiny text slice is already green.
- The declared and actual workload shapes are both `1 x 128`.
- The baseline is stable enough to compare against.
- Ascend NPU remains blocked and is not part of the optimized claim.

## Hypothesis

The tiny text slice is likely spending most of its time in decoder dispatch and
activation movement rather than in the attention kernel itself.

## Change

Config diff for the candidate run:

```yaml
optimization_change:
  name: decoder-dispatch-tuning
  scope: tiny-text-only
  change:
    - enable cached graph reuse for the steady-state window
    - keep batch size at 1 and context length at 128
    - preserve BF16 and the same recipe/config pins
  excluded:
    - multimodal inputs
    - distributed execution
    - long-context expansion
    - Ascend NPU execution
```

The change is intentionally small so the effect can be attributed to one
optimization hypothesis.

## Profiler Evidence

| Field | Baseline | Candidate |
|-------|----------|-----------|
| Throughput | 43.2 tokens/s | 47.4 tokens/s |
| Latency p50 | 18.4 ms | 17.1 ms |
| Latency p95 | 25.1 ms | 23.8 ms |
| Peak memory | 9.6 GiB | 9.5 GiB |
| Device utilization | 78% | 81% |
| Compile overhead | 0.0 s | 0.0 s |
| Runtime stability | passed | passed |

Profiler artifact links:

- `logs/performance/veomni-minimax-m3-tiny-text-gpu-baseline.log`
- `artifacts/performance/veomni-minimax-m3-tiny-text-gpu-baseline.trace.json`
- `logs/performance/veomni-minimax-m3-tiny-text-gpu-candidate.log`
- `artifacts/performance/veomni-minimax-m3-tiny-text-gpu-candidate.trace.json`

Profiler tool:

- `torch.profiler`

Capture window:

- Tiny text warm run followed by the same steady-state window on the declared
  `1 x 128` slice.

## Before / After Interpretation

The candidate improves throughput and slightly reduces latency without changing
the declared workload or the precision policy. Memory is effectively flat, so
the likely gain is reduced dispatch overhead rather than a major memory-shape
shift.

## Correctness Regression Check

Scoped correctness checks were re-run on the candidate path:

- Tiny text forward parity remained green.
- Shape and dtype stayed pinned to the declared slice.
- No new backend support claim was introduced.

## Acceptance Decision

Decision: accepted for the tiny text GPU slice.

Why:

- The candidate improved throughput by more than the declared threshold.
- Correctness stayed green for the same slice.
- The workload stayed unchanged, so the comparison is attributable.

## Rollback Criteria

Rollback if any of the following occurs:

- Tiny text correctness regresses.
- Throughput falls below the baseline by the accepted threshold.
- Latency or runtime stability worsens beyond the declared guard.
- The candidate config can no longer reproduce on the same hardware and recipe.

## Explicit Optimization Dimensions

The tiny text slice is the first optimization surface, but these dimensions stay
visible for follow-up work:

- MiniMax Sparse Attention.
- Long-context memory planning.
- Distributed communication behavior.
- Precision policy.
- Compile and graph behavior.
- Ascend NPU runtime readiness.

## Ascend NPU Status

Ascend NPU remains blocked evidence only. Do not treat this example as an NPU
support claim.

- `docs/ops/ascend-npu-runtime.md`
- Root-approved `npu-smi` evidence is still missing.
- CANN activation is still missing.
- Matching PyTorch/PTA or `torch_npu` evidence is still missing.
- Tensor smoke and the smallest VeOmni smoke are still not reached.

## Review Checklist

- [x] Baseline and candidate are named.
- [x] The change is small and reproducible.
- [x] Profiler evidence is attached.
- [x] Before/after metrics are recorded.
- [x] Correctness regression is checked before acceptance.
- [x] Rollback criteria are explicit.
- [x] Ascend NPU remains blocked evidence only.
- [x] MSA, long-context, distributed, precision, and compile/graph dimensions
  stay visible.
