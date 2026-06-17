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
| Correctness validation harness | `docs/framework/correctness-validation-harness.md` | Ordered smoke, unit, parity, runtime, and transition gates before optimization. |
| Accuracy and drift signoff | `docs/framework/accuracy-drift-signoff.md` | Versioned baseline, metric, threshold, drift, and reviewer signoff contract. |
| Validation result lifecycle | `docs/framework/validation-result-lifecycle.md` | How validation results block or allow lifecycle transitions and backlog gates. |
| Performance profile spec | `docs/framework/performance-profile-spec.md` | Backend-neutral performance evidence contract for profiler-backed optimization work. |
| Optimization loop | `docs/framework/optimization-loop.md` | Baseline-to-rollback optimization contract after correctness is green. |

## Schemas

| Schema | File | Use |
|--------|------|-----|
| Migration manifest schema | `docs/framework/schemas/migration-manifest.schema.json` | Machine-checkable migration manifest skeleton. |
| Validation recipe schema | `docs/framework/schemas/validation-recipe.schema.json` | Machine-checkable correctness validation recipe skeleton. |
| Accuracy signoff schema | `docs/framework/schemas/accuracy-signoff.schema.json` | Machine-checkable accuracy and drift signoff skeleton. |
| Performance profile schema | `docs/framework/schemas/performance-profile.schema.json` | Machine-checkable performance evidence skeleton for optimization baselines and comparisons. |

## Examples

| Example | File | Use |
|---------|------|-----|
| VeOmni + MiniMax M3 manifest | `docs/framework/examples/veomni-minimax-m3.manifest.yaml` | Shows how a hard model case fills manifest fields. |
| VeOmni + MiniMax M3 capabilities | `docs/framework/examples/veomni-minimax-m3-capabilities.md` | Shows GPU/NPU capability rows and runtime blockers. |
| VeOmni + MiniMax M3 validation recipe | `docs/framework/examples/veomni-minimax-m3.validation-recipe.yaml` | Shows first-slice correctness gates and blocked NPU runtime status. |
| VeOmni + MiniMax M3 accuracy signoff | `docs/framework/examples/veomni-minimax-m3.accuracy-signoff.yaml` | Shows pending accuracy/drift signoff fields without claiming support. |
| VeOmni + MiniMax M3 performance profile | `docs/framework/examples/veomni-minimax-m3.performance-profile.yaml` | Shows the tiny text optimization baseline, backend-specific counters, and blocked Ascend NPU evidence. |
| VeOmni + MiniMax M3 optimization report | `docs/framework/examples/veomni-minimax-m3.optimization-report.md` | Shows a complete tiny text optimization attempt with profiler evidence and rollback criteria. |

The MiniMax M3 examples are case-specific. They demonstrate the generic
contracts, but detailed evidence remains under `docs/cases/veomni-minimax-m3/`.
They are a worked example; support claims still require validation and runtime
evidence for the exact scope being claimed.

## Templates

| Template | File | Use |
|----------|------|-----|
| Template pack | `docs/framework/template-pack.md` | Lifecycle catalog for applying the framework templates in order. |
| Migration intake | `docs/framework/migration-intake-template.md` | Start a new migration with facts, assumptions, backend scope, validation gates, and signoff. |
| Gap analysis | `docs/framework/gap-analysis-template.md` | Turn intake findings into owner-layer gaps with severity, first action, and blocking status. |
| Migration manifest template | `docs/framework/migration-manifest-template.yaml` | Placeholder YAML skeleton aligned to the manifest spec and schema. |
| Backend capability matrix template | `docs/framework/backend-capability-matrix-template.md` | Placeholder capability rows for backend support, blockers, validation targets, precision, memory, communication, compile behavior, and profiler hooks. |
| Validation recipe template | `docs/framework/validation-recipe-template.yaml` | Placeholder correctness recipe distinguishing failed correctness from blocked runtime. |
| Accuracy signoff template | `docs/framework/accuracy-signoff-template.yaml` | Placeholder baseline, threshold, drift, lifecycle decision, and reviewer signoff artifact. |
| Performance profile template | `docs/framework/performance-profile-template.yaml` | Placeholder workload, metrics, profiler, runtime stability, and regression guard artifact. |
| Optimization report template | `docs/framework/optimization-report-template.md` | Lab-notebook template for profiler-backed before/after optimization attempts. |
| Migration handoff template | `docs/framework/migration-handoff-template.md` | Final readiness review with evidence inventory, lifecycle state, support scope, blockers, reusable deltas, and reviewer signoff. |

## How To Start A New Migration

Use `docs/framework/next-case-guide.md` as the operator runbook before writing a
new implementation plan.

1. Start with `docs/framework/template-pack.md` to choose the lifecycle path and templates.
2. Create a migration intake using `docs/framework/migration-intake-template.md`.
3. Convert intake findings into owner-layer gaps with `docs/framework/gap-analysis-template.md` and `docs/framework/backlog-taxonomy.md`.
4. Fill or draft a migration manifest using `docs/framework/migration-manifest-template.yaml`, `docs/framework/migration-manifest-spec.md`, and the schema in `docs/framework/schemas/migration-manifest.schema.json`.
5. Separate responsibilities with `docs/framework/adapter-contracts.md`.
6. Record backend support and blockers with `docs/framework/backend-capability-matrix-template.md` and `docs/framework/backend-capability-matrix.md`.
7. Move through `docs/framework/migration-lifecycle.md` with evidence-gated transitions.
8. Build correctness gates with `docs/framework/validation-recipe-template.yaml`, `docs/framework/correctness-validation-harness.md`, and `docs/framework/schemas/validation-recipe.schema.json`.
9. Use `docs/framework/validation-result-lifecycle.md` to decide whether validation results block lifecycle transitions.
10. Record accuracy and drift signoff with `docs/framework/accuracy-signoff-template.yaml`, `docs/framework/accuracy-drift-signoff.md`, and `docs/framework/schemas/accuracy-signoff.schema.json`.
11. Capture performance baselines with `docs/framework/performance-profile-template.yaml`, `docs/framework/performance-profile-spec.md`, and `docs/framework/schemas/performance-profile.schema.json`.
12. Start optimization only after correctness evidence exists, then use `docs/framework/optimization-report-template.md` and `docs/framework/optimization-loop.md` for profiler-backed before/after metrics.
13. Close the migration with `docs/framework/migration-handoff-template.md`, including evidence inventory, lifecycle state, validation status, accuracy status, performance status, backend capability status, unresolved blockers, reusable deltas, and reviewer signoff.
14. Use the MiniMax M3 examples and `docs/cases/veomni-minimax-m3/reference-case.md` as worked examples of support claims, runtime evidence, and blocked evidence handling, not as generic defaults.

## Phase 2 Contract Boundary

Phase 2 defines architecture and contract documents. It does not claim that
MiniMax M3 runs in VeOmni or that Ascend NPU execution is ready. Current NPU
support remains blocked until driver/DCMI, CANN, PyTorch/PTA or `torch_npu`,
tensor smoke, and VeOmni smoke gates pass.

## Phase 3 Validation Boundary

Phase 3 defines validation and signoff contracts. It does not claim that
MiniMax M3 accuracy has been measured, that a VeOmni adapter exists, or that
NPU execution is ready. Accuracy signoff examples remain pending or blocked
until baseline, dataset, thresholds, candidate execution, and backend evidence
are captured.

## Related Case Evidence

- `docs/cases/veomni-minimax-m3/reference-case.md`
- `docs/cases/veomni-minimax-m3/intake.md`
- `docs/cases/veomni-minimax-m3/assumptions.md`
- `docs/cases/veomni-minimax-m3/gap-analysis.md`
- `docs/cases/veomni-minimax-m3/reusable-deltas.md`
- `docs/cases/veomni-minimax-m3/npu-runtime-evidence.md`
- `docs/ops/ascend-npu-runtime.md`
