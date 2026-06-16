# Phase 5: Reference Case Packaging - Research

## RESEARCH COMPLETE

**Question answered:** What do we need to know to plan Phase 5 well?

Phase 5 is a documentation-and-operating-guidance phase. The core planning need is not new model research; it is organizing the artifacts from Phases 1-4 into a reusable template pack while preserving the line between the MiniMax M3 reference case and the generic AI Infra Migration Framework contracts.

## Inputs Read

[VERIFIED: local docs]

- `./AGENTS.md`
- `.planning/ROADMAP.md`
- `.planning/REQUIREMENTS.md`
- `.planning/STATE.md`
- `.planning/phases/aimf-05-reference-case-packaging/05-CONTEXT.md`
- `.planning/phases/aimf-04-backend-optimization-loop/04-CONTEXT.md`
- `docs/framework/README.md`
- `docs/framework/migration-intake-template.md`
- `docs/framework/gap-analysis-template.md`
- `docs/framework/migration-manifest-spec.md`
- `docs/framework/adapter-contracts.md`
- `docs/framework/backend-capability-matrix.md`
- `docs/framework/migration-lifecycle.md`
- `docs/framework/backlog-taxonomy.md`
- `docs/framework/correctness-validation-harness.md`
- `docs/framework/accuracy-drift-signoff.md`
- `docs/framework/validation-result-lifecycle.md`
- `docs/framework/performance-profile-spec.md`
- `docs/framework/optimization-loop.md`
- `docs/framework/schemas/migration-manifest.schema.json`
- `docs/framework/schemas/validation-recipe.schema.json`
- `docs/framework/schemas/accuracy-signoff.schema.json`
- `docs/framework/schemas/performance-profile.schema.json`
- `docs/framework/examples/veomni-minimax-m3.manifest.yaml`
- `docs/framework/examples/veomni-minimax-m3.validation-recipe.yaml`
- `docs/framework/examples/veomni-minimax-m3.accuracy-signoff.yaml`
- `docs/framework/examples/veomni-minimax-m3.performance-profile.yaml`
- `docs/framework/examples/veomni-minimax-m3.optimization-report.md`
- `docs/framework/examples/veomni-minimax-m3-capabilities.md`
- `docs/cases/veomni-minimax-m3/intake.md`
- `docs/cases/veomni-minimax-m3/assumptions.md`
- `docs/cases/veomni-minimax-m3/gap-analysis.md`
- `docs/cases/veomni-minimax-m3/reusable-deltas.md`
- `docs/cases/veomni-minimax-m3/npu-runtime-evidence.md`
- `docs/ops/ascend-npu-runtime.md`

## User Constraints

[VERIFIED: local docs]

- Phase 5 must produce templates for future model/framework migrations.
- MiniMax M3-specific findings must remain separate from reusable framework contracts.
- The next migration must be able to start from documented intake, gap, validation, and optimization templates.
- Existing `migration-intake-template.md` and `gap-analysis-template.md` remain valid entry points and should not be broken.
- The template pack should cover the full lifecycle: intake, gap analysis, manifest, backend capability matrix, validation recipe, accuracy/drift signoff, performance profile, optimization report, and final handoff/readiness review.
- Runtime setup blockers, especially Ascend NPU blockers, must route through backend runtime evidence and capability matrices rather than becoming model implementation failures.
- Do not claim MiniMax M3 support, VeOmni runtime execution, or Ascend NPU readiness from this packaging phase.

## Project Constraints

[VERIFIED: local docs]

- Migration logic must remain reusable and not tangled with one framework trainer or one accelerator runtime.
- Accuracy and performance gates are required before migration signoff.
- GPU and NPU support must be modeled through backend capability descriptors, not scattered conditional branches.
- The framework should fit infra-team workflows: specs, manifests, adapters, reproducible recipes, profiling reports, CI gates, and reviewable evidence.
- CANN/PTA/NPU execution claims require explicit runtime evidence before support can be stated.
- Future phases should use feature branches and PRs; Phase 5 should not mutate repository publishing docs unless needed for the handoff.

## Relevant Existing Docs And Patterns

[VERIFIED: local docs]

- `docs/framework/README.md` already acts as the framework navigation hub. It should be updated into the main entry point for templates, examples, and the next-case guide.
- `docs/framework/migration-intake-template.md` and `docs/framework/gap-analysis-template.md` already cover the first two lifecycle templates. Phase 5 should package them, not replace them with incompatible paths.
- `docs/framework/migration-manifest-spec.md` plus `docs/framework/schemas/migration-manifest.schema.json` already provide the manifest contract. A template wrapper can point users to those canonical files instead of duplicating all fields.
- `docs/framework/correctness-validation-harness.md` and `docs/framework/schemas/validation-recipe.schema.json` already define correctness recipe structure and blocked runtime status.
- `docs/framework/accuracy-drift-signoff.md` and `docs/framework/schemas/accuracy-signoff.schema.json` already define accuracy signoff structure.
- `docs/framework/performance-profile-spec.md` and `docs/framework/schemas/performance-profile.schema.json` already define performance evidence.
- `docs/framework/optimization-loop.md` and `docs/framework/examples/veomni-minimax-m3.optimization-report.md` already define an optimization report pattern.
- `docs/framework/backend-capability-matrix.md` already models unsupported, blocked, emulated, native, and optimized states.
- `docs/cases/veomni-minimax-m3/reusable-deltas.md` already explains which MiniMax M3 lessons were promoted into generic templates and which must remain case-specific.
- `docs/cases/veomni-minimax-m3/npu-runtime-evidence.md` and `docs/ops/ascend-npu-runtime.md` already capture the blocked Ascend runtime path and must remain conservative.

## Planning Implications

[ASSUMED]

Phase 5 should split into the two roadmap plans:

1. **05-01: Package reusable migration templates.** This plan should add the missing lifecycle templates and an index/catalog that makes the package usable. It should preserve existing template paths and link to schemas/examples. Candidate additions:
   - `docs/framework/template-pack.md`
   - `docs/framework/validation-recipe-template.md`
   - `docs/framework/accuracy-signoff-template.md`
   - `docs/framework/performance-profile-template.md`
   - `docs/framework/optimization-report-template.md`
   - `docs/framework/migration-handoff-template.md`
   - updates to `docs/framework/README.md`

2. **05-02: Produce next-case handoff guide.** This plan should create the operating runbook for the next migration case and update the MiniMax M3 reference case so readers know what to reuse and what not to copy blindly. Candidate additions:
   - `docs/framework/next-case-guide.md`
   - `docs/cases/veomni-minimax-m3/reference-case.md`
   - updates to `docs/cases/veomni-minimax-m3/reusable-deltas.md`
   - updates to `docs/framework/README.md`

The two plans can run in separate waves because the next-case guide should point to the final template pack created by 05-01.

## Template Pack Design Guidance

[VERIFIED: local docs + ASSUMED packaging structure]

The reusable template pack should be a curated lifecycle sequence, not a pile of disconnected files. It should tell the reader:

1. Start with intake and source facts.
2. Convert unknowns into owner-layer gaps.
3. Draft or validate the migration manifest.
4. Record backend capability states and runtime blockers.
5. Define correctness validation recipes.
6. Define accuracy and drift signoff.
7. Capture performance profiles only after correctness gates are green.
8. Run optimization experiments with before/after evidence and rollback criteria.
9. Close the migration with a handoff/readiness review that includes reusable deltas.

Recommended fields for new templates:

- Status and owner.
- Source artifacts and canonical refs.
- Scope, first slice, and non-goals.
- Evidence required before status movement.
- Backend/runtime status, including blocked runtime evidence.
- Review checklist.
- Links to schema/spec/example when a machine-checkable contract already exists.

## Case Packaging Guidance

[VERIFIED: local docs + ASSUMED packaging structure]

The MiniMax M3 reference case should be packaged as a worked example, not as a generic default. A useful `reference-case.md` should:

- List the case artifacts and their purpose.
- Explain which files are reusable templates versus case evidence.
- Summarize what the case proved and what remains blocked.
- State that no MiniMax M3, VeOmni, or Ascend NPU runtime support claim is made by the documentation alone.
- Point to `reusable-deltas.md` for generalized lessons.
- Point to `npu-runtime-evidence.md` for blocked runtime gates.

## Next-Case Guide Guidance

[ASSUMED]

The next-case guide should be operator-facing and concise. It should include:

- A "copy these templates first" section.
- A 0-to-1 ordered checklist from intake through handoff.
- A first-slice selection rubric.
- A backend runtime blocker routing rule.
- A validation-before-optimization rule.
- A review checklist for case/generic separation.
- A final "ready to plan" checklist that maps to GSD phase planning.

## Risks To Avoid

[VERIFIED: local docs]

- Do not move existing template files without preserving links from earlier phase plans and summaries.
- Do not inline schema field definitions repeatedly and create competing sources of truth.
- Do not make MSA, 1M context, multimodality, or Ascend A2/910B requirements generic defaults.
- Do not mark blocked NPU runtime evidence as a failed correctness gate or as support.
- Do not make a next-case guide that only recaps MiniMax M3; it must be reusable operating guidance.

## Recommended Verification

[ASSUMED]

- `rg "template-pack|validation-recipe-template|accuracy-signoff-template|performance-profile-template|optimization-report-template|migration-handoff-template|next-case-guide" docs/framework/README.md docs/framework`
- `rg "MiniMax M3|VeOmni|MSA|1M|Ascend A2|910B" docs/framework/*template*.md docs/framework/template-pack.md` should show no generic default claims except explicit example links or "do not copy blindly" guidance.
- `rg "blocked|runtime evidence|capability matrix|correctness|accuracy|performance|optimization|handoff" docs/framework/next-case-guide.md docs/framework/migration-handoff-template.md`
- Existing schemas should continue to parse: `python3 -m json.tool docs/framework/schemas/migration-manifest.schema.json >/dev/null`, and equivalent checks for validation, accuracy, and performance schemas.

## Open Planning Questions

[ASSUMED]

- Whether the template pack should introduce a `docs/framework/templates/` subdirectory or preserve flat `docs/framework/*-template.md` naming. The current repo already uses flat names, so flat naming plus a catalog has the least churn.
- Whether manifest and backend capability need full template files or just catalog entries pointing at their specs. A concise wrapper is enough if the canonical spec already has review checklists.
- Whether the MiniMax M3 case index should live as `reference-case.md` or an update to `intake.md`. A separate `reference-case.md` is clearer for packaging.
