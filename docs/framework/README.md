# AI Infra Migration Framework Contracts

This directory contains the reusable contracts for AI Infra migration work:
manifest, adapters, backend capability modeling, lifecycle states, backlog
taxonomy, and templates for future cases.

## Core Contracts

| Contract | File | Use |
|----------|------|-----|
| Migration manifest spec | `docs/framework/migration-manifest-spec.md` | Human-readable manifest field definitions and review checklist. |
| Migration manifest schema | `docs/framework/schemas/migration-manifest.schema.json` | Machine-checkable schema skeleton for manifest validation. |
| Adapter contracts | `docs/framework/adapter-contracts.md` | Ownership boundaries for ModelSpec, FrameworkAdapter, BackendAdapter, DataAdapter, Recipe, ValidationSuite, and OptimizationLoop. |
| Backend capability matrix | `docs/framework/backend-capability-matrix.md` | Capability maturity, runtime blockers, backend dimensions, and support evidence. |
| Migration lifecycle | `docs/framework/migration-lifecycle.md` | Evidence-gated migration states from Intake to Production Ready. |
| Backlog taxonomy | `docs/framework/backlog-taxonomy.md` | Owner-layer backlog fields, severity, evidence, first action, blocker, and lifecycle mapping. |

## Examples

| Example | File | Use |
|---------|------|-----|
| VeOmni + MiniMax M3 manifest | `docs/framework/examples/veomni-minimax-m3.manifest.yaml` | Shows how a hard model case fills manifest fields. |
| VeOmni + MiniMax M3 capabilities | `docs/framework/examples/veomni-minimax-m3-capabilities.md` | Shows GPU/NPU capability rows and runtime blockers. |

The MiniMax M3 examples are case-specific. They demonstrate the generic
contracts, but detailed evidence remains under `docs/cases/veomni-minimax-m3/`.

## Templates

| Template | File | Use |
|----------|------|-----|
| Migration intake | `docs/framework/migration-intake-template.md` | Start a new migration with facts, assumptions, backend scope, validation gates, and signoff. |
| Gap analysis | `docs/framework/gap-analysis-template.md` | Turn intake findings into owner-layer gaps with severity, first action, and blocking status. |

## How To Start A New Migration

1. Create a migration intake using `docs/framework/migration-intake-template.md`.
2. Fill or draft a migration manifest using `docs/framework/migration-manifest-spec.md` and the schema in `docs/framework/schemas/migration-manifest.schema.json`.
3. Separate responsibilities with `docs/framework/adapter-contracts.md`.
4. Record backend support and blockers with `docs/framework/backend-capability-matrix.md`.
5. Convert gaps into backlog items using `docs/framework/backlog-taxonomy.md`.
6. Move through `docs/framework/migration-lifecycle.md` with evidence-gated transitions.
7. Build correctness and accuracy gates before scale or optimization work.
8. Start optimization only after correctness evidence exists, and record profiler-backed before/after metrics.

## Phase 2 Contract Boundary

Phase 2 defines architecture and contract documents. It does not claim that
MiniMax M3 runs in VeOmni or that Ascend NPU execution is ready. Current NPU
support remains blocked until driver/DCMI, CANN, PyTorch/PTA or `torch_npu`,
tensor smoke, and VeOmni smoke gates pass.

## Related Case Evidence

- `docs/cases/veomni-minimax-m3/intake.md`
- `docs/cases/veomni-minimax-m3/assumptions.md`
- `docs/cases/veomni-minimax-m3/gap-analysis.md`
- `docs/cases/veomni-minimax-m3/reusable-deltas.md`
- `docs/cases/veomni-minimax-m3/npu-runtime-evidence.md`
- `docs/ops/ascend-npu-runtime.md`
