# Phase 03 Discussion Log

## 2026-06-16 - Context Setup

Phase 3 starts after Phase 2 completed the manifest, adapter, backend capability, lifecycle, and backlog contracts.

Key alignment:

- The harness must prevent "runs once" from being mistaken for migration completion.
- Correctness gates come before optimization and performance tuning.
- Accuracy signoff is a separate, versioned contract from tiny smoke or parity.
- MiniMax M3 remains the proving case, especially tokenizer/processor drift, checkpoint layout, MSA support, long context, multimodality, precision, and NPU blockers.
- Ascend NPU validation is gated by root-level device evidence, CANN, PTA/`torch_npu`, tensor smoke, and VeOmni smoke; until those pass, NPU validation rows remain blocked rather than failed.

Planning implications:

1. Split Phase 3 into correctness harness work and accuracy/drift signoff work, matching the existing roadmap.
2. Make validation artifacts reusable for future model/framework migrations.
3. Ensure every validation result can feed lifecycle transition decisions and backlog acceptance gates.

No user clarification is required before planning.
