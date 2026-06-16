# Phase 2: Core Migration Architecture - Research

## RESEARCH COMPLETE

**Question answered:** What do we need to know to plan Phase 2 well?

Phase 2 is a documentation-and-contract phase. The useful work is to convert Phase 1's MiniMax M3 evidence into stable framework artifacts that can guide later implementation: manifest/schema, adapter boundary specs, capability matrix, lifecycle states, and backlog taxonomy. The phase should avoid runtime implementation unless a small scaffold is necessary to make contracts concrete.

## Inputs Read

- `.planning/phases/aimf-02-core-migration-architecture/02-CONTEXT.md`
- `.planning/REQUIREMENTS.md`
- `.planning/ROADMAP.md`
- `.planning/STATE.md`
- `.planning/phases/01-veomni-minimax-m3-intake/01-CONTEXT.md`
- `docs/cases/veomni-minimax-m3/intake.md`
- `docs/cases/veomni-minimax-m3/assumptions.md`
- `docs/cases/veomni-minimax-m3/gap-analysis.md`
- `docs/cases/veomni-minimax-m3/reusable-deltas.md`
- `docs/cases/veomni-minimax-m3/npu-runtime-evidence.md`
- `docs/framework/migration-intake-template.md`
- `docs/framework/gap-analysis-template.md`
- `docs/ops/ascend-npu-runtime.md`

## Planning Implications

### 1. Manifest And Domain Model

The manifest should start as a reviewable specification plus example YAML, not runtime code. The planned output should include:

- `docs/framework/migration-manifest-spec.md`: field definitions, owner layers, evidence fields, and MiniMax M3 examples.
- `docs/framework/examples/veomni-minimax-m3.manifest.yaml`: example manifest grounded in Phase 1 evidence.
- `docs/framework/schemas/migration-manifest.schema.json`: a Draft 2020-12-compatible schema skeleton that enforces required sections and enumerations.

The schema must cover `ModelSpec`, `FrameworkAdapter`, `BackendAdapter`, `DataAdapter`, `Recipe`, `ValidationSuite`, and `OptimizationLoop`. It should include ownership and evidence pointers so each field can be reviewed and traced to case docs or external sources.

### 2. Adapter Boundaries And Capability Matrix

The architecture should separate concerns by owner layer:

- `ModelSpec`: model semantics independent of framework/backend.
- `FrameworkAdapter`: target framework hooks, registration, builders, recipes, and distributed execution.
- `BackendAdapter`: accelerator/runtime/operator/precision/communication/profiling capabilities.
- `DataAdapter`: tokenizer, processor, dataset, checkpoint, fixture, and reference-output contracts.
- `ValidationSuite`: smoke, parity, correctness, accuracy, drift, and status gates.
- `OptimizationLoop`: profiler evidence, bottleneck hypotheses, before/after metrics, regression guard, and rollback criteria.

The capability matrix should use maturity states `unsupported`, `emulated`, `native`, and `optimized`. Runtime blockers such as root-only `npu-smi`, missing CANN, missing `torch_npu`, or missing Docker device access should be evidence/blocker metadata, not a replacement maturity enum.

Recommended planned output:

- `docs/framework/adapter-contracts.md`
- `docs/framework/backend-capability-matrix.md`
- `docs/framework/examples/veomni-minimax-m3-capabilities.md`

### 3. Lifecycle And Backlog Taxonomy

The migration lifecycle should preserve the requirement-defined states:

`Intake -> Gap Analysis -> Adapter Build -> Correctness -> Scale -> Optimize -> Accuracy Signoff -> Production Ready`

Each transition should name the blocking evidence required to move forward. Backlog items should carry `owner_layer`, `severity`, `evidence`, `first_action`, `blocks_slice`, `backend_scope`, `dependencies`, `acceptance_gate`, and `status_transition`.

Recommended planned output:

- `docs/framework/migration-lifecycle.md`
- `docs/framework/backlog-taxonomy.md`

## Validation Architecture

This phase is mainly verified through source assertions and deterministic content checks.

| Dimension | Verification Approach |
|-----------|------------------------|
| Requirement coverage | Each Phase 2 requirement ID appears in at least one plan frontmatter and in the relevant contract docs. |
| Manifest correctness | Schema/spec/example contain the required owner layers, source/target, model/backend/data/recipe/validation/optimization sections, ownership, evidence, and MiniMax M3 example values. |
| Adapter separation | Adapter docs explicitly state which layer owns model semantics, framework hooks, backend capabilities, data artifacts, validation, and optimization; docs forbid accelerator conditionals in model logic. |
| Capability states | Capability matrix defines `unsupported`, `emulated`, `native`, and `optimized`; runtime blockers are represented separately. |
| Lifecycle gating | Lifecycle doc includes all required states and evidence gates; backlog taxonomy maps gaps to owner layer, severity, first action, and blocking status. |
| NPU constraints | Backend docs reference Ascend root-only `npu-smi`, CANN official download source, `torch_npu`/PTA gate, and VeOmni A2/910B Docker guide. |

Recommended automated checks:

- `test -f` for every planned artifact.
- `rg` assertions for required state names, owner layers, capability states, and MiniMax M3-specific values.
- `python3 -m json.tool docs/framework/schemas/migration-manifest.schema.json` once the JSON schema file exists.

## Risks For Planning

- **Over-abstracting too early:** Phase 2 must keep MiniMax M3 example values in every contract so the framework does not drift into generic architecture prose.
- **Mixing capability maturity and runtime readiness:** NPU runtime blockers need to be explicit without breaking the required maturity enum.
- **Creating code before contracts stabilize:** A runtime package can wait unless the plan finds a very small scaffold with clear verification value.
- **Dropping validation/optimization from architecture:** Phase 3 and 4 own detailed harnesses, but Phase 2 still needs fields and lifecycle gates that leave room for them.

## Recommended Plan Shape

1. **Domain model and manifest schema**: create manifest spec, JSON schema, and MiniMax M3 example manifest.
2. **Adapter boundaries and capability descriptors**: define adapter contracts, backend capability matrix, and MiniMax M3 capability example.
3. **Lifecycle and backlog taxonomy**: define migration lifecycle states, evidence-gated transitions, and backlog item taxonomy.
