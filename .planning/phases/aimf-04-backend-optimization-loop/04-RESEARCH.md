# Phase 4: Backend Optimization Loop - Research

## RESEARCH COMPLETE

**Question answered:** What do we need to know to plan Phase 4 well?

Phase 4 is a documentation-and-contract phase that turns correctness-green
migrations into profiler-backed optimization work. The plan should define a
repeatable performance profile schema, a reviewable optimization experiment
record, and the lifecycle/backlog wiring that lets backend tuning stay behind
`BackendAdapter` and `OptimizationLoop` boundaries instead of leaking into model
logic.

## Inputs Read

[VERIFIED: local docs]

- `./AGENTS.md`
- `.planning/phases/aimf-04-backend-optimization-loop/04-CONTEXT.md`
- `.planning/REQUIREMENTS.md`
- `.planning/STATE.md`
- `.planning/ROADMAP.md`
- `docs/framework/adapter-contracts.md`
- `docs/framework/backend-capability-matrix.md`
- `docs/framework/validation-result-lifecycle.md`
- `docs/framework/correctness-validation-harness.md`
- `docs/framework/backlog-taxonomy.md`
- `docs/framework/migration-manifest-spec.md`
- `docs/ops/ascend-npu-runtime.md`
- `.planning/phases/aimf-03-correctness-and-accuracy-harness/03-CONTEXT.md`
- `docs/framework/migration-lifecycle.md`
- `docs/framework/README.md`
- `docs/framework/examples/veomni-minimax-m3-capabilities.md`
- `docs/framework/examples/veomni-minimax-m3.validation-recipe.yaml`
- `docs/framework/examples/veomni-minimax-m3.manifest.yaml`
- `docs/framework/accuracy-drift-signoff.md`
- `docs/framework/gap-analysis-template.md`

## User Constraints

[VERIFIED: local docs]

- Phase 4 must stay downstream of correctness and accuracy: optimization work
  only starts after semantic validation is green.
- Performance evidence must cover throughput, latency, memory, utilization,
  compile overhead, runtime stability, backend, dtype, hardware, sequence or
  context length, batch shape, and recipe/config identity.
- Every optimization attempt must record baseline, hypothesis, change,
  expected bottleneck, before/after metrics, correctness regression check,
  acceptance result, and rollback criteria.
- GPU and NPU differences must remain backend capabilities with comparable
  top-level fields plus backend-specific metric namespaces.
- Ascend NPU optimization cannot be claimed until root-approved `npu-smi`,
  CANN activation, matching PyTorch/PTA or `torch_npu`, tensor smoke, and the
  smallest VeOmni smoke all have evidence.
- The first optimization slice should stay aligned with the Phase 3 tiny text
  path; MSA, long-context, distributed, precision, and compile behavior must
  remain visible as explicit optimization dimensions.

## Project Constraints

[VERIFIED: local docs]

- Migration logic must not be tangled with one framework's trainer or one
  accelerator's runtime.
- Every supported path needs explicit accuracy and performance gates; "runs
  once" is not signoff.
- GPU and NPU should be modeled as backend capabilities, not scattered
  conditionals in model code.
- MiniMax M3 likely needs sparse-attention/MSA handling, long-context memory
  planning, and multimodal input contracts.
- The framework should fit specs, manifests, adapters, reproducible recipes,
  profiling reports, and CI gates.
- The repository target is `Kirrito-k423/AutoModelMigrate`; future phases should
  use feature branches and PRs.
- Ascend NPU setup knowledge must remain tied to the reusable runtime skill and
  evidence record; do not claim NPU execution support without the documented
  gates.
- For Ascend A2/910B Docker, prefer VeOmni's upstream A2 guide before inventing
  a custom container recipe.

## Relevant Existing Docs And Patterns

[VERIFIED: local docs]

- `docs/framework/adapter-contracts.md` already assigns `OptimizationLoop`
  ownership for baseline, profiling, bottleneck analysis, before/after metrics,
  regression guard, and rollback. Phase 4 should extend that ownership with a
  concrete artifact shape, not redefine the contract.
- `docs/framework/backend-capability-matrix.md` already defines `optimized`
  as the state that requires profiler output, before/after metrics, and
  rollback criteria. It also separates runtime blockers from maturity states.
- `docs/framework/validation-result-lifecycle.md` already says `Scale -> Optimize`
  requires correctness plus a performance baseline recipe, and
  `Optimize -> Accuracy Signoff` requires before/after evidence with correctness
  still green.
- `docs/framework/correctness-validation-harness.md` already isolates backend
  runtime gates from correctness gates. That means Phase 4 must not convert NPU
  readiness problems into semantic failures.
- `docs/framework/backlog-taxonomy.md` already has an `OptimizationLoop`
  owner layer and a lifecycle mapping that can absorb unresolved performance
  gaps.
- `docs/framework/migration-manifest-spec.md` already includes an
  `optimization` section in the manifest, so Phase 4 should refine that section
  rather than add a separate competing workflow.
- `docs/framework/examples/veomni-minimax-m3-capabilities.md` already models
  MSA, long-context, precision, distributed communication, graph/compile, and
  profiler hooks as capability rows. This is the right place to carry `native`
  versus `optimized` movement.
- `docs/framework/examples/veomni-minimax-m3.validation-recipe.yaml` already
  distinguishes correctness gates from backend runtime gates. That makes it a
  good anchor for the "baseline recipe" input to Phase 4, but not the final
  performance schema.
- `docs/ops/ascend-npu-runtime.md` already records the current NPU blockers:
  root-only device visibility, missing CANN, missing `torch_npu`, tensor smoke
  not reached, and VeOmni smoke not reached.
- `.planning/phases/aimf-03-correctness-and-accuracy-harness/03-CONTEXT.md`
  and `docs/framework/migration-lifecycle.md` already establish the gate order
  that Phase 4 must preserve.

## Recommended Docs And Artifacts To Create Or Update

[ASSUMED]

1. Create `docs/framework/performance-profile-spec.md` as the human-readable
   contract for performance evidence.
2. Create `docs/framework/schemas/performance-profile.schema.json` for
   machine-checkable performance records.
3. Create `docs/framework/optimization-loop.md` to define the experiment loop,
   regression guard rules, rollback criteria, and backend-to-capability links.
4. Create `docs/framework/examples/veomni-minimax-m3.performance-profile.yaml`
   as the first profile example on the Phase 3 tiny text slice.
5. Create `docs/framework/examples/veomni-minimax-m3.optimization-report.md`
   or `.yaml` as the first experiment record with baseline, hypothesis, change,
   before/after metrics, and rollback fields.
6. Update `docs/framework/backend-capability-matrix.md` with guidance for
   `optimized` transitions that point to profiler evidence and regression
   guards.
7. Update `docs/framework/migration-manifest-spec.md` so the `optimization`
   section names the performance profile, experiment record, and links to
   backend capability rows.
8. Update `docs/framework/migration-lifecycle.md` and
   `docs/framework/validation-result-lifecycle.md` only if additional clarity is
   needed for the `Scale -> Optimize` and `Optimize -> Accuracy Signoff`
   evidence chain.
9. Update `docs/framework/backlog-taxonomy.md` with optimization-specific item
   examples for unresolved bottlenecks, regression risk, and backend-scoped
   tuning work.
10. Update `docs/framework/README.md` so Phase 4 artifacts are discoverable
    next to the manifest, validation, and accuracy docs.

## Performance Profile Schema Guidance

[VERIFIED: local docs]

The schema should be common across backends, with backend-specific metric
namespaces under a dedicated object. The top-level fields should be stable and
comparably named so GPU and NPU reports can be reviewed side by side.

Minimum recommended fields:

- `profile_id`, `version`, `migration_id`, and manifest reference.
- `baseline_id`, `candidate_id`, and the exact recipe/config identity.
- `backend`, `dtype`, `hardware`, `framework_revision`, and environment
  activation details.
- `workload`: batch shape, sequence/context length, modality, and scale target.
- `metrics`: throughput, latency, memory, utilization, compile overhead,
  stability, and optionally backend-specific counters.
- `profiler`: tool name, capture command, capture window, and raw artifact
  links.
- `bottleneck`: named bottleneck hypothesis and the observed evidence.
- `regression_guard`: correctness checks, acceptance thresholds, and rerun
  criteria.
- `status`: `pending`, `passed`, `failed`, or `blocked` for the profile
  record.

[ASSUMED]

Design rules the planner should keep:

- Keep the top-level schema backend-neutral.
- Put vendor counters and tool-specific names under a backend namespace.
- Treat compile overhead and runtime stability as required fields, not optional
  notes.
- Preserve the exact manifest/recipe/config references so performance results
  stay reproducible.
- Record both the declared workload and the actual captured workload, because
  optimization claims are brittle when shape or batch drift.

## Optimization Experiment And Report Workflow Guidance

[VERIFIED: local docs]

The experiment workflow should be a short, explicit chain:

1. Confirm correctness gates are green for the exact slice being optimized.
2. Capture a baseline performance profile for that slice.
3. Identify a bottleneck and state the hypothesis in one sentence.
4. Apply one optimization change at a time, with the config diff recorded.
5. Re-run the same workload and capture before/after metrics.
6. Re-run the correctness regression check for the declared scope.
7. Record the acceptance decision and rollback criteria.
8. Update the backend capability row to `optimized` only when evidence links
   are complete.
9. If the bottleneck remains unresolved, create or update a backlog item with
   owner, severity, evidence, first action, backend scope, acceptance gate, and
   lifecycle transition.

[ASSUMED]

The report should read like a lab notebook for infra work:

- Start with the workload, backend, and baseline identity.
- State the bottleneck hypothesis before the change.
- Show the exact optimization delta.
- Show the same workload before and after the change.
- Close with a correctness check, not just speed numbers.
- Include rollback criteria that a reviewer can act on without guessing.

## Backend Capability Integration Guidance

[VERIFIED: local docs]

- Keep performance tuning data in `BackendAdapter` capability descriptors and
  capability matrix rows.
- Do not move backend-specific tuning logic into model semantics or
  framework-local conditionals.
- Use the same capability row to track `native` -> `optimized` movement, with
  profiler output and regression guards as the evidence requirement.
- Keep runtime blockers separate from capability maturity so the project can
  still describe incomplete NPU readiness without claiming support.
- For MiniMax M3, keep MSA, long-context memory, distributed communication,
  precision policy, graph/compile constraints, and profiler hooks as explicit
  capability dimensions.

[ASSUMED]

The planner should expect at least three backend comparison scopes:

1. Reference or dense fallback.
2. Native backend path.
3. Optimized backend path.

That split will make the first optimization plan legible even before Ascend NPU
execution is available.

## Validation Architecture

[VERIFIED: local docs]

Phase 4 planning should itself be checked through doc-level evidence, not
runtime claims.

Planned validation for the phase artifacts should include:

- Schema presence and required-field checks for the new performance profile
  artifact.
- `rg`-based assertions that `OptimizationLoop`, profiler evidence, rollback
  criteria, backend-specific namespaces, and regression guards are named in the
  docs.
- Cross-links from the optimization record to the manifest, backend capability
  row, and lifecycle transition it affects.
- Example coverage for the Phase 3 tiny text slice, so the first optimization
  case is concrete and bounded.
- Explicit blocked-state handling for Ascend NPU so missing runtime evidence is
  visible but does not masquerade as a model failure.

[ASSUMED]

Planning gates that should exist before implementation starts:

1. Performance profile schema draft is reviewable.
2. Optimization report template is reviewable.
3. Backend capability row updates are specified for `optimized` movement.
4. Rollback and regression guard rules are explicit enough for a reviewer to
   judge one experiment without asking for a re-architecture.

## Risks And Pitfalls

[VERIFIED: local docs]

- Optimizing before correctness would violate the migration lifecycle and can
  hide semantic regressions.
- Treating backend blockers as performance failures would blur runtime readiness
  with model behavior.
- Letting GPU and NPU use incompatible performance fields would make comparison
  and review harder than necessary.
- Allowing a speed-only success criterion would break the project requirement
  that performance and accuracy evidence both matter.
- Overfitting Phase 4 to Ascend NPU would leave the framework unable to support
  other backends cleanly.

[ASSUMED]

- A single vendor profiler may not cover every backend, so the schema should
  stay extensible without becoming vague.
- The first optimization slice may produce a blocked or partial profile on NPU
  while GPU/reference work proceeds.
- If the Phase 3 tiny text path is not representative enough for later tuning,
  the phase will need a second profile example for a larger but still bounded
  workload.

## Recommended Plan Split

[VERIFIED: local docs]

The roadmap already splits Phase 4 into `04-01` and `04-02`. Those should stay
cleanly separated:

### 04-01: Define Profiling And Performance Report Schema

What this plan should establish:

- A human-readable performance profile spec.
- A machine-checkable schema for performance profiles.
- A MiniMax M3 example profile on the Phase 3 tiny text slice.
- Stable common metrics and backend-specific metric namespaces.
- Explicit linkages to manifest, recipe, backend capability, and lifecycle
  fields.

Why this split matters:

- The team needs a reviewable artifact shape before they can discuss tuning
  policy.
- The schema should make GPU/NPU comparisons possible without assuming the same
  counters exist on every backend.
- A profile schema can be planned and reviewed without any runtime execution.

### 04-02: Define Backend-Specific Optimization Workflow

What this plan should establish:

- An `OptimizationLoop` workflow doc or report template.
- The sequence baseline -> hypothesis -> change -> before/after metrics ->
  correctness regression check -> acceptance/rollback.
- Rules for turning unresolved bottlenecks into backlog items.
- Rules for moving a backend capability row to `optimized`.
- The first MiniMax M3 optimization example, still anchored to the tiny text
  path and still explicit about MSA, long-context, distributed, precision, and
  NPU scopes.

Why this split matters:

- The workflow depends on the schema, not the other way around.
- The workflow is where review decisions, rollback logic, and capability
  maturity updates become concrete.
- Separating the two keeps the planning docs modular and easier to reuse for
  later migrations.

## Planning Notes

[ASSUMED]

- Treat the current Ascend NPU state as a capability blocker, not as a reason to
  pause non-NPU optimization planning.
- Keep the first optimization example narrow enough to be measurable and broad
  enough to exercise the new schema.
- Prefer Markdown specs plus small YAML examples before introducing any code.
- Preserve the same owner layers used everywhere else: `ModelSpec`,
  `FrameworkAdapter`, `BackendAdapter`, `DataAdapter`, `Recipe`,
  `ValidationSuite`, and `OptimizationLoop`.

## Fallback Note

[VERIFIED: local docs]

This research memo was produced via the default agent fallback because the
specialized `gsd-phase-researcher` agent type failed model resolution.

