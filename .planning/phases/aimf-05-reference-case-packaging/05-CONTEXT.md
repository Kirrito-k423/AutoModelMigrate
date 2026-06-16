# Phase 5: Reference Case Packaging - Context

**Gathered:** 2026-06-16
**Status:** Ready for planning

<domain>
## Phase Boundary

Phase 5 packages the completed VeOmni + MiniMax M3 reference case into a reusable migration template pack and a next-case operating guide. It does not implement MiniMax M3 support, run VeOmni, install NPU runtime dependencies, or claim new backend capability. Its job is to make the framework usable by the next migration team without losing the evidence gates and case-specific caveats learned in Phases 1-4.

</domain>

<decisions>
## Implementation Decisions

### Template Pack Boundary
- **D-05-01:** The reusable template pack should cover the full migration lifecycle: intake, gap analysis, manifest, backend capability matrix, validation recipe, accuracy/drift signoff, performance profile, optimization report, and final handoff/readiness review.
- **D-05-02:** Existing Phase 1 templates (`migration-intake-template.md`, `gap-analysis-template.md`) remain valid compatibility entry points. Phase 5 may add a clearer template index and additional lifecycle templates rather than breaking existing links.
- **D-05-03:** Templates must stay generic and placeholder-driven. MiniMax M3 facts should appear only as example references or case evidence, not as required generic fields.

### Case-Specific Versus Reusable Split
- **D-05-04:** MiniMax M3, VeOmni, Ascend A2/910B, MSA, 1M context, multimodal fixtures, and current host NPU blocker details stay under `docs/cases/veomni-minimax-m3/` or `docs/framework/examples/`.
- **D-05-05:** Generic contracts stay under `docs/framework/` and must describe owner layers, lifecycle gates, evidence requirements, backend capability states, and validation/optimization thresholds without naming one model as the default.
- **D-05-06:** Any new example should label its support status conservatively (`pending`, `blocked`, `reference-only`, or equivalent) when no runtime evidence exists.

### Next-Case Operating Guide
- **D-05-07:** The next migration guide should be an operator-facing runbook: where to copy templates from, what order to fill them in, what evidence gates block status transitions, and how to decide the first vertical slice.
- **D-05-08:** The guide should explicitly route runtime setup blockers, especially Ascend NPU blockers, through backend runtime evidence and capability matrices instead of treating them as model implementation failures.
- **D-05-09:** The guide should include a review checklist that proves a new migration has separated case facts, reusable contract updates, validation evidence, optimization evidence, and unresolved backlog items.

### Documentation Shape
- **D-05-10:** Prefer Markdown templates and indexes because the repository is currently documentation/spec-first. JSON/YAML schemas and examples should be referenced, not duplicated, when a template already has a machine-checkable companion.
- **D-05-11:** The framework README should become the navigation hub for the packaged templates, examples, and next-case guide.
- **D-05-12:** Verification should be evidence-based: check that every required lifecycle template is linked, MiniMax-specific evidence remains case-scoped, and a new migration can start from a documented sequence.

### the agent's Discretion
- The planner may choose exact filenames and whether additional templates live directly under `docs/framework/` or a `docs/framework/templates/` subdirectory, as long as existing links remain usable.
- The planner may decide whether to create full standalone template files for schema-backed artifacts or concise Markdown wrappers that point to the canonical schema/spec/example.

</decisions>

<canonical_refs>
## Canonical References

**Downstream agents MUST read these before planning or implementing.**

### Project Scope
- `.planning/ROADMAP.md` - Phase 5 goal, dependency on Phase 4, success criteria, and plan list.
- `.planning/REQUIREMENTS.md` - `M3-04`, `FLOW-01`, `FLOW-02`, `FLOW-03`, `VAL-01` through `VAL-04`, and `ACC-01` through `ACC-04` traceability.
- `.planning/PROJECT.md` - framework constraints: reusable architecture, validation gates, backend capability modeling, and NPU runtime evidence rules.

### Prior Phase Context
- `.planning/phases/01-veomni-minimax-m3-intake/01-03-SUMMARY.md` - original reusable intake and gap template extraction decisions.
- `.planning/phases/aimf-02-core-migration-architecture/02-CONTEXT.md` - reusable contract boundary and note that Phase 5 owns template packaging.
- `.planning/phases/aimf-03-correctness-and-accuracy-harness/03-CONTEXT.md` - validation recipe, accuracy signoff, and correctness-before-optimization decisions.
- `.planning/phases/aimf-04-backend-optimization-loop/04-CONTEXT.md` - performance profile, optimization report, and backend capability evidence decisions.

### Framework Contracts And Existing Templates
- `docs/framework/README.md` - current framework index and "How To Start A New Migration" sequence.
- `docs/framework/migration-intake-template.md` - existing generic intake template.
- `docs/framework/gap-analysis-template.md` - existing generic gap analysis template.
- `docs/framework/migration-manifest-spec.md` - manifest field contract and review checklist.
- `docs/framework/adapter-contracts.md` - ownership boundaries for ModelSpec, adapters, ValidationSuite, and OptimizationLoop.
- `docs/framework/backend-capability-matrix.md` - backend capability maturity, blockers, and support evidence.
- `docs/framework/migration-lifecycle.md` - evidence-gated migration states.
- `docs/framework/backlog-taxonomy.md` - owner-layer backlog taxonomy.
- `docs/framework/correctness-validation-harness.md` - correctness validation gates.
- `docs/framework/accuracy-drift-signoff.md` - accuracy and drift signoff contract.
- `docs/framework/validation-result-lifecycle.md` - validation result transition semantics.
- `docs/framework/performance-profile-spec.md` - performance evidence contract.
- `docs/framework/optimization-loop.md` - profiler-backed optimization workflow.

### Schemas And Examples
- `docs/framework/schemas/migration-manifest.schema.json` - machine-checkable manifest skeleton.
- `docs/framework/schemas/validation-recipe.schema.json` - machine-checkable validation recipe skeleton.
- `docs/framework/schemas/accuracy-signoff.schema.json` - machine-checkable accuracy signoff skeleton.
- `docs/framework/schemas/performance-profile.schema.json` - machine-checkable performance profile skeleton.
- `docs/framework/examples/veomni-minimax-m3.manifest.yaml` - MiniMax M3 manifest example.
- `docs/framework/examples/veomni-minimax-m3.validation-recipe.yaml` - MiniMax M3 validation recipe example.
- `docs/framework/examples/veomni-minimax-m3.accuracy-signoff.yaml` - MiniMax M3 accuracy signoff example.
- `docs/framework/examples/veomni-minimax-m3.performance-profile.yaml` - MiniMax M3 performance profile example.
- `docs/framework/examples/veomni-minimax-m3.optimization-report.md` - MiniMax M3 optimization report example.
- `docs/framework/examples/veomni-minimax-m3-capabilities.md` - MiniMax M3 backend capability example.

### Case Evidence
- `docs/cases/veomni-minimax-m3/intake.md` - case-specific intake facts and first slice.
- `docs/cases/veomni-minimax-m3/assumptions.md` - case-specific facts, assumptions, risks, and validation needs.
- `docs/cases/veomni-minimax-m3/gap-analysis.md` - case-specific owner-layer gap map.
- `docs/cases/veomni-minimax-m3/reusable-deltas.md` - what was promoted from MiniMax M3 into generic templates and what stayed case-specific.
- `docs/cases/veomni-minimax-m3/npu-runtime-evidence.md` - case-specific Ascend NPU blockers.
- `docs/ops/ascend-npu-runtime.md` - reusable Ascend runtime readiness gates.

</canonical_refs>

<code_context>
## Existing Code Insights

### Reusable Assets
- `docs/framework/migration-intake-template.md` and `docs/framework/gap-analysis-template.md`: already cover the first two lifecycle templates and should not be invalidated.
- `docs/framework/schemas/*.schema.json`: provide machine-checkable contracts that new template docs can reference.
- `docs/framework/examples/veomni-minimax-m3.*`: show how a complex case fills the reusable contracts.
- `docs/cases/veomni-minimax-m3/reusable-deltas.md`: already documents the separation rule between generic template fields and MiniMax-only facts.

### Established Patterns
- Framework artifacts are Markdown specs with tables, evidence expectations, examples, and review checklists.
- Examples are case-specific and conservative about support claims when runtime evidence is missing.
- Backend and NPU claims are gated by explicit runtime evidence; blocked runtime setup is recorded as a BackendAdapter/capability state.

### Integration Points
- Update `docs/framework/README.md` so users can find the complete template pack and next-case guide.
- Add or update template files in `docs/framework/` while preserving existing root-level template paths.
- Add next-case guidance that references both framework contracts and the MiniMax M3 case evidence without mixing their responsibilities.

</code_context>

<specifics>
## Specific Ideas

- Treat the MiniMax M3 case as the worked example and the template pack as the reusable product.
- The next-case guide should answer "what do I fill out first?" and "what evidence blocks moving forward?" rather than restating every schema field.
- Include an explicit final handoff/readiness template so future migrations close with evidence, unresolved blockers, and reusable deltas.

</specifics>

<deferred>
## Deferred Ideas

None - discussion stayed within phase scope.

</deferred>

---

*Phase: 5-Reference Case Packaging*
*Context gathered: 2026-06-16*
