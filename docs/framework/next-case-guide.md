# Next Case Guide

## When To Use This

Use this guide when a new migration is ready to move from idea to planned
engineering work. It is operator-facing: the goal is to gather enough evidence
to be ready to plan, not to write adapter code first.

This guide applies to migrations for models, algorithms, datasets, training
features, inference features, serving paths, and backend or accelerator stacks.

## Setup Inputs

Collect these inputs before opening an implementation plan:

| Input | Required Evidence |
|-------|-------------------|
| Source | Repository, artifact, checkpoint, dataset, paper, service, or runtime reference with version or revision. |
| Target | Framework, runtime, serving stack, or backend receiving support. |
| First slice | Smallest useful vertical path that can produce evidence. |
| Backend scope | CPU, GPU, NPU, mixed, or reference-only execution scope. |
| Runtime evidence | Existing device, package, driver, container, and permission facts. |
| Validation owner | Person or team responsible for correctness, accuracy, performance, and handoff evidence. |

## Template Order

Start with `template-pack.md`; it is the catalog for the rest of the workflow.

1. `docs/framework/template-pack.md` - choose the lifecycle path and confirm the
   source-of-truth rules.
2. `docs/framework/migration-intake-template.md` - record intake facts,
   assumptions, first slice, non-goals, and initial gates.
3. `docs/framework/gap-analysis-template.md` - convert unknowns into owner-layer
   gaps and first actions.
4. `docs/framework/migration-manifest-template.yaml` - draft the manifest once
   source, target, scope, adapters, validation, and evidence are named.
5. `docs/framework/backend-capability-matrix-template.md` - record backend
   capability rows, support status, blockers, and validation targets.
6. `docs/framework/validation-recipe-template.yaml` - write correctness gates
   before treating a run as migrated support.
7. `docs/framework/accuracy-signoff-template.yaml` - declare baseline, accuracy
   metric, threshold, drift policy, and reviewer.
8. `docs/framework/performance-profile-template.yaml` - capture workload,
   backend, dtype, hardware, profiler, metrics, and regression guard.
9. `docs/framework/optimization-report-template.md` - record each optimization
   attempt as before/after evidence.
10. `docs/framework/migration-handoff-template.md` - close with lifecycle state,
    validation status, accuracy status, performance status, backend capability,
    unresolved blockers, reusable deltas, and reviewer signoff.

## First Slice Rubric

A good first slice is small, evidence-producing, and hard to fake.

| Question | Good Answer |
|----------|-------------|
| Does it touch source and target? | Yes, at least through config, data, construction, or a tiny execution path. |
| Does it expose model or feature semantics? | Yes, through fixture shape, dtype, output, loss, or invariant evidence. |
| Does it avoid full-scale work? | Yes, large checkpoints, long contexts, distributed recipes, and vendor kernels can be staged later. |
| Does it name non-goals? | Yes, especially backends, modalities, scale targets, and optimization work outside the slice. |
| Does it produce a blocker if it cannot run? | Yes, the blocker must name the owner layer and next evidence action. |

## Runtime Blocker Routing

A runtime blocker belongs in backend evidence and capability rows until the
runtime can actually run. Do not turn a missing driver, package, device
permission, container, or vendor toolkit into a model correctness failure.

| Runtime blocker | Route it to | Do not route it to |
|-----------------|-------------|--------------------|
| Driver or device visibility missing | Backend capability matrix and runtime evidence doc. | ModelSpec semantics. |
| Toolkit or framework backend package missing | BackendAdapter blocker and install runbook. | ValidationSuite failure. |
| Tensor smoke not reached | Backend runtime gate. | Accuracy signoff. |
| Framework smoke not reached | BackendAdapter plus FrameworkAdapter gap, depending on cause. | Optimization report. |
| Profiler unavailable | Performance profile blocker. | Correctness result. |

## correctness-before-optimization

Optimization starts only after the scoped correctness gate is green. A faster
path with missing parity, missing drift threshold, or changed semantics is not an
optimization success. If an optimization changes model behavior, route the work
back to Correctness before any accuracy or performance claim.

## Lifecycle Movement

| Move | Required Evidence |
|------|-------------------|
| Intake -> Gap Analysis | Intake exists, assumptions are explicit, first slice and non-goals are named. |
| Gap Analysis -> Adapter Build | Owner-layer gaps, severity, blockers, and first actions are recorded. |
| Adapter Build -> Correctness | Adapter surface, data contract, backend capability rows, and validation recipe are ready. |
| Correctness -> Scale | Smoke/parity gates pass for the scoped workload, or blockers are explicit and scoped. |
| Scale -> Optimize | Correctness remains green and a baseline performance profile exists for the same slice. |
| Optimize -> Accuracy | Before/after metrics, correctness regression check, and rollback criteria exist. |
| Accuracy -> Handoff | Baseline, threshold, drift result, reviewer decision, and support scope are recorded. |

## Review Checklist

- [ ] Case facts live under the case directory or example files.
- [ ] Generic contracts live under `docs/framework/` and stay model-neutral.
- [ ] Runtime blockers are represented as backend evidence and capability rows.
- [ ] Validation gates distinguish failed correctness from blocked runtime.
- [ ] Accuracy has a baseline, metric, threshold, drift policy, and reviewer.
- [ ] Performance has workload identity, backend, dtype, hardware, profiler
  evidence, and regression guard.
- [ ] Optimization reports include before/after metrics, correctness regression
  checks, acceptance decision, rollback criteria, and backlog handoff.
- [ ] Handoff records unresolved blockers and reusable deltas.

## ready to plan Checklist

The next implementation plan is ready only when all of these are true:

- [ ] The intake names source, target, first slice, non-goals, and assumptions.
- [ ] Gap analysis has owner-layer rows and first actions for blocking gaps.
- [ ] Manifest status reflects the true lifecycle state.
- [ ] Backend capability rows include runtime blocker status where relevant.
- [ ] Validation recipe names fixtures, commands, expected evidence, and
  transition-blocking results.
- [ ] Accuracy and performance evidence requirements are explicit, even when
  deferred.
- [ ] Optimization is deferred until correctness evidence exists.
- [ ] Handoff expectations are known before work starts.
