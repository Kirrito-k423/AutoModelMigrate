# AI Infra Migration Template Pack

## Purpose

This pack turns the framework contracts into a repeatable migration workflow.
Use it for a new model, framework, dataset, training feature, inference feature,
or backend migration. The templates are generic and placeholder-driven; case
facts belong in case evidence files or in the examples linked below.

`docs/framework/migration-intake-template.md` and
`docs/framework/gap-analysis-template.md` remain the stable compatibility entry
points for starting a migration.

## Lifecycle Sequence

| Stage | Template | Canonical contract | Required evidence | Supports transition |
|-------|----------|--------------------|-------------------|---------------------|
| Migration Intake | `docs/framework/migration-intake-template.md` | `docs/framework/migration-lifecycle.md` | Source, target, scope, first slice, non-goals, assumptions, backend scope, initial validation gates. | Request -> Intake |
| Gap Analysis | `docs/framework/gap-analysis-template.md` | `docs/framework/backlog-taxonomy.md` | Owner-layer gaps, severity, first action, blocker status, dependencies, evidence links. | Intake -> Gap Analysis |
| Migration Manifest | `docs/framework/migration-manifest-template.yaml` | `docs/framework/migration-manifest-spec.md`, `docs/framework/schemas/migration-manifest.schema.json`, `docs/framework/examples/veomni-minimax-m3.manifest.yaml` | Machine-checkable source, target, model or feature contract, adapter surfaces, backend matrix, validation, optimization, ownership, evidence. | Gap Analysis -> Adapter Build |
| Backend Capability Matrix | `docs/framework/backend-capability-matrix-template.md` | `docs/framework/backend-capability-matrix.md`, `docs/framework/examples/veomni-minimax-m3-capabilities.md` | Backend status rows, runtime blockers, precision, memory, communication, compile behavior, profiler hooks, validation targets. | Adapter Build -> Correctness |
| Validation Recipe | `docs/framework/validation-recipe-template.yaml` | `docs/framework/correctness-validation-harness.md`, `docs/framework/schemas/validation-recipe.schema.json`, `docs/framework/examples/veomni-minimax-m3.validation-recipe.yaml` | Fixtures, gates, commands, expected evidence, result states, blocked runtime versus failed correctness. | Adapter Build -> Correctness and Correctness -> Scale |
| Accuracy and Drift Signoff | `docs/framework/accuracy-signoff-template.yaml` | `docs/framework/accuracy-drift-signoff.md`, `docs/framework/schemas/accuracy-signoff.schema.json`, `docs/framework/examples/veomni-minimax-m3.accuracy-signoff.yaml` | Baseline, candidate, evaluation set, metrics, threshold, drift policy, lifecycle decision, reviewer decision. | Optimize -> Accuracy Signoff |
| Performance Profile | `docs/framework/performance-profile-template.yaml` | `docs/framework/performance-profile-spec.md`, `docs/framework/schemas/performance-profile.schema.json`, `docs/framework/examples/veomni-minimax-m3.performance-profile.yaml` | Workload identity, backend, dtype, hardware, throughput, latency, memory, utilization, compile overhead, runtime stability, profiler artifacts, regression guard. | Scale -> Optimize |
| Optimization Report | `docs/framework/optimization-report-template.md` | `docs/framework/optimization-loop.md`, `docs/framework/examples/veomni-minimax-m3.optimization-report.md` | Baseline, hypothesis, config diff, before/after metrics, correctness regression check, acceptance decision, rollback, backlog handoff. | Optimize -> Accuracy Signoff |
| Migration Handoff | `docs/framework/migration-handoff-template.md` | `docs/framework/migration-lifecycle.md`, `docs/framework/validation-result-lifecycle.md` | Evidence inventory, lifecycle state, validation status, accuracy status, performance status, backend capability, unresolved blockers, reusable deltas, reviewer signoff. | Accuracy Signoff -> Production Ready |

The example links above are case-specific. Treat them as worked examples, not as
defaults to copy into a new migration.

## Recommended Use

1. Start with Migration Intake and record the smallest useful first slice.
2. Run Gap Analysis and convert findings into owner-layer backlog items.
3. Draft the Migration Manifest and keep schema-backed fields machine-checkable.
4. Fill the Backend Capability Matrix before making backend support claims.
5. Write the Validation Recipe before implementation changes are treated as
   complete.
6. Capture Accuracy and Drift Signoff only after baseline, threshold, and
   candidate evidence exist.
7. Capture the Performance Profile before optimization changes.
8. Record each Optimization Report as a lab notebook with rollback criteria.
9. Close with Migration Handoff only when evidence, blockers, and reusable
   deltas are reviewable.

## Source Of Truth Rules

- Markdown templates explain workflow and review expectations.
- YAML templates are skeletons that should stay aligned with the schema files.
- Specs and schemas are the canonical contract for fields and status values.
- Examples are case-specific evidence and must not become generic defaults.
- A blocked runtime is not a correctness failure unless the semantic check ran
  and failed.
