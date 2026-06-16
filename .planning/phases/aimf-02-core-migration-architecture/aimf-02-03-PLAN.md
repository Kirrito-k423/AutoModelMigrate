---
phase: aimf-02-core-migration-architecture
plan: "03"
type: execute
wave: 2
depends_on:
  - aimf-02-01
  - aimf-02-02
files_modified:
  - docs/framework/migration-lifecycle.md
  - docs/framework/backlog-taxonomy.md
  - docs/framework/README.md
autonomous: true
requirements:
  - ARCH-04
  - FLOW-01
  - FLOW-02
  - FLOW-04
  - ACC-01
  - ACC-03
user_setup: []
must_haves:
  truths:
    - D-12: Migration status follows Intake, Gap Analysis, Adapter Build, Correctness, Scale, Optimize, Accuracy Signoff, Production Ready.
    - D-13: Status transitions are evidence-gated.
    - D-14: Backlog items carry owner layer, severity, evidence, first action, blocking status, dependencies, backend scope, and acceptance gate.
    - D-15: Backend readiness can be blocked while model/framework work continues on a reference path.
    - D-16: Optimization work is downstream of correctness and accuracy contracts.
  artifacts:
    - docs/framework/migration-lifecycle.md
    - docs/framework/backlog-taxonomy.md
    - docs/framework/README.md
  key_links:
    - Lifecycle gates must preserve future Phase 3 validation and Phase 4 optimization responsibilities.
---

<objective>
Define the migration lifecycle and backlog taxonomy.

Purpose: Make migration progress evidence-gated from intake to production readiness, while preserving owner-layer backlog fields that let teams plan targeted adapter, data, backend, validation, and optimization work.
Output: Lifecycle doc, backlog taxonomy doc, and framework docs index.
</objective>

<context>
@.planning/phases/aimf-02-core-migration-architecture/02-CONTEXT.md
@.planning/phases/aimf-02-core-migration-architecture/aimf-02-RESEARCH.md
@.planning/phases/aimf-02-core-migration-architecture/aimf-02-VALIDATION.md
@docs/framework/gap-analysis-template.md
@docs/cases/veomni-minimax-m3/gap-analysis.md
@docs/cases/veomni-minimax-m3/reusable-deltas.md
</context>

<threat_model>
| Threat | Severity | Mitigation |
|--------|----------|------------|
| T-02-05 Lifecycle lets work advance to optimization or production without correctness, accuracy, backend, and documentation evidence. | high | Define required evidence per transition and make `Correctness`, `Optimize`, `Accuracy Signoff`, and `Production Ready` gates explicit. |
| T-02-06 Backlog taxonomy loses owner-layer evidence and recreates generic task lists. | medium | Require owner layer, severity, evidence, first action, blocker, dependency, backend scope, and acceptance gate fields. |
</threat_model>

## Artifacts this phase produces

- New file: `docs/framework/migration-lifecycle.md`
- New file: `docs/framework/backlog-taxonomy.md`
- New file: `docs/framework/README.md`
- Lifecycle states: `Intake`, `Gap Analysis`, `Adapter Build`, `Correctness`, `Scale`, `Optimize`, `Accuracy Signoff`, `Production Ready`
- Backlog fields: `owner_layer`, `severity`, `evidence`, `first_action`, `blocks_slice`, `dependencies`, `backend_scope`, `acceptance_gate`, `status_transition`

<tasks>

<task type="auto">
  <name>Task 1: Write evidence-gated lifecycle</name>
  <files>docs/framework/migration-lifecycle.md</files>
  <read_first>.planning/phases/aimf-02-core-migration-architecture/02-CONTEXT.md, docs/cases/veomni-minimax-m3/gap-analysis.md, docs/framework/migration-intake-template.md</read_first>
  <action>Create `docs/framework/migration-lifecycle.md` with the states `Intake`, `Gap Analysis`, `Adapter Build`, `Correctness`, `Scale`, `Optimize`, `Accuracy Signoff`, and `Production Ready`. For each state, define purpose, entry evidence, exit evidence, allowed next states, blocking conditions, and MiniMax M3 example. Explicitly state that NPU runtime can be blocked while reference artifact/framework work continues, and that optimization cannot start before correctness evidence exists.</action>
  <verify>rg "Intake|Gap Analysis|Adapter Build|Correctness|Scale|Optimize|Accuracy Signoff|Production Ready|entry evidence|exit evidence|MiniMax M3|NPU runtime|correctness" docs/framework/migration-lifecycle.md</verify>
  <acceptance_criteria>
    - Lifecycle doc contains all FLOW-04 states.
    - Each state has entry and exit evidence.
    - Doc prevents Optimize and Production Ready from being reached without correctness/accuracy/backend/documentation evidence.
    - Doc includes MiniMax M3 examples.
  </acceptance_criteria>
  <done>Migration lifecycle is evidence-gated and ready for future phases.</done>
</task>

<task type="auto">
  <name>Task 2: Write backlog taxonomy</name>
  <files>docs/framework/backlog-taxonomy.md</files>
  <read_first>docs/framework/gap-analysis-template.md, docs/cases/veomni-minimax-m3/gap-analysis.md, docs/framework/migration-lifecycle.md</read_first>
  <action>Create `docs/framework/backlog-taxonomy.md` defining backlog item fields `owner_layer`, `severity`, `evidence`, `first_action`, `blocks_slice`, `dependencies`, `backend_scope`, `acceptance_gate`, and `status_transition`. Include owner-layer values `ModelSpec`, `FrameworkAdapter`, `BackendAdapter`, `DataAdapter`, `Recipe`, `ValidationSuite`, and `OptimizationLoop`; severity values `Critical`, `High`, `Medium`, `Low`; and examples derived from MiniMax M3 gaps such as config schema not pinned, VeOmni model registration unknown, MSA backend support unknown, tokenizer special tokens not inventoried, and NPU runtime blocked.</action>
  <verify>rg "owner_layer|severity|evidence|first_action|blocks_slice|dependencies|backend_scope|acceptance_gate|status_transition|ModelSpec|FrameworkAdapter|BackendAdapter|DataAdapter|ValidationSuite|OptimizationLoop|Critical|High|Medium|Low" docs/framework/backlog-taxonomy.md</verify>
  <acceptance_criteria>
    - Backlog taxonomy includes every field named in D-14.
    - Owner layers cover all architecture contracts.
    - Examples use MiniMax M3 gaps and preserve blocking status.
    - Taxonomy links backlog items to lifecycle transitions.
  </acceptance_criteria>
  <done>Backlog taxonomy can turn gap analysis into targeted execution work.</done>
</task>

<task type="auto">
  <name>Task 3: Create framework docs index</name>
  <files>docs/framework/README.md</files>
  <read_first>docs/framework/migration-manifest-spec.md, docs/framework/adapter-contracts.md, docs/framework/backend-capability-matrix.md, docs/framework/migration-lifecycle.md, docs/framework/backlog-taxonomy.md</read_first>
  <action>Create `docs/framework/README.md` as an index for Phase 2 architecture contracts. Link to the manifest spec, JSON schema, MiniMax M3 manifest example, adapter contracts, backend capability matrix, MiniMax M3 capability example, migration lifecycle, backlog taxonomy, migration intake template, and gap analysis template. Add a short "How to start a new migration" section with ordered steps: intake manifest, gap analysis, adapter/capability backlog, correctness gates, scale/optimize, accuracy signoff.</action>
  <verify>rg "migration-manifest-spec|migration-manifest.schema.json|veomni-minimax-m3.manifest.yaml|adapter-contracts|backend-capability-matrix|veomni-minimax-m3-capabilities|migration-lifecycle|backlog-taxonomy|migration-intake-template|gap-analysis-template|How to start a new migration" docs/framework/README.md</verify>
  <acceptance_criteria>
    - README links every Phase 2 framework artifact.
    - README explains the order for starting a new migration.
    - README points MiniMax M3 examples to the case directory and keeps them separate from generic contracts.
  </acceptance_criteria>
  <done>Framework architecture docs have a navigable entry point.</done>
</task>

</tasks>

<verification>
Before declaring plan complete:
- [ ] `rg "Intake|Gap Analysis|Adapter Build|Correctness|Scale|Optimize|Accuracy Signoff|Production Ready" docs/framework/migration-lifecycle.md`
- [ ] `rg "owner_layer|severity|evidence|first_action|blocks_slice|acceptance_gate" docs/framework/backlog-taxonomy.md`
- [ ] `rg "migration-manifest-spec|adapter-contracts|backend-capability-matrix|migration-lifecycle|backlog-taxonomy" docs/framework/README.md`
- [ ] Confirm lifecycle text states optimization follows correctness and backend readiness can be blocked independently.
</verification>

<success_criteria>
- Migration lifecycle covers required states and status transitions.
- Backlog taxonomy preserves owner-layer evidence and blocking status.
- Framework docs index makes Phase 2 architecture contracts discoverable.
</success_criteria>

<output>
After completion, create `.planning/phases/aimf-02-core-migration-architecture/aimf-02-03-SUMMARY.md`
</output>
