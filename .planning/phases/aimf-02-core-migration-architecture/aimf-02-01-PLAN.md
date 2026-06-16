---
phase: aimf-02-core-migration-architecture
plan: "01"
type: execute
wave: 1
depends_on: []
files_modified:
  - docs/framework/migration-manifest-spec.md
  - docs/framework/schemas/migration-manifest.schema.json
  - docs/framework/examples/veomni-minimax-m3.manifest.yaml
autonomous: true
requirements:
  - ARCH-01
  - ARCH-02
  - FLOW-01
  - ACC-03
user_setup: []
must_haves:
  truths:
    - D-01: Phase 2 produces a schema-first migration manifest with docs and MiniMax M3 example values.
    - D-02: The manifest represents source/target framework, model family, backend, data/checkpoint contracts, precision policy, validation gates, ownership, and evidence links.
    - D-03: ModelSpec captures model semantics such as MSA, long context, multimodality, checkpoint metadata, tokenizer/processor contracts, dtype policy, and assumptions.
    - D-04: Advertised capability and staged validation targets are separate fields.
    - D-05: Each manifest section has owner layer and evidence pointers.
  artifacts:
    - docs/framework/migration-manifest-spec.md
    - docs/framework/schemas/migration-manifest.schema.json
    - docs/framework/examples/veomni-minimax-m3.manifest.yaml
  key_links:
    - Example values must trace back to `docs/cases/veomni-minimax-m3/intake.md`, `assumptions.md`, and `reusable-deltas.md`.
---

<objective>
Define the core migration domain model and manifest schema.

Purpose: Make new AI Infra migrations start from a machine-checkable contract that captures source/target context, owner layers, evidence, and MiniMax M3-style complexity before adapter work begins.
Output: Manifest specification, JSON Schema skeleton, and a MiniMax M3 example manifest.
</objective>

<context>
@.planning/phases/aimf-02-core-migration-architecture/02-CONTEXT.md
@.planning/phases/aimf-02-core-migration-architecture/aimf-02-RESEARCH.md
@.planning/phases/aimf-02-core-migration-architecture/aimf-02-VALIDATION.md
@.planning/REQUIREMENTS.md
@docs/framework/migration-intake-template.md
@docs/cases/veomni-minimax-m3/intake.md
@docs/cases/veomni-minimax-m3/assumptions.md
@docs/cases/veomni-minimax-m3/reusable-deltas.md
</context>

<threat_model>
| Threat | Severity | Mitigation |
|--------|----------|------------|
| T-02-01 Example manifest accidentally embeds secrets, private paths, or tokens. | high | Use placeholder owners, public URLs, and evidence links only; do not include credentials, PATs, private hostnames, or root command output. |
| T-02-02 Manifest schema allows unsupported NPU/runtime claims without evidence. | high | Require `owner_layer`, `status`, `evidence`, and `blockers` fields for backend/capability entries. |
</threat_model>

## Artifacts this phase produces

- New file: `docs/framework/migration-manifest-spec.md`
- New file: `docs/framework/schemas/migration-manifest.schema.json`
- New file: `docs/framework/examples/veomni-minimax-m3.manifest.yaml`
- Manifest sections: `migration`, `source`, `target`, `model_spec`, `framework_adapter`, `backend_matrix`, `data_contract`, `recipe`, `validation`, `optimization`, `ownership`, `evidence`
- Schema fields: `owner_layer`, `status`, `evidence`, `blockers`, `validation_targets`, `assumptions`
- Capability maturity enum values referenced by manifest: `unsupported`, `emulated`, `native`, `optimized`

<tasks>

<task type="auto">
  <name>Task 1: Create manifest JSON Schema skeleton</name>
  <files>docs/framework/schemas/migration-manifest.schema.json</files>
  <read_first>.planning/phases/aimf-02-core-migration-architecture/02-CONTEXT.md, .planning/phases/aimf-02-core-migration-architecture/aimf-02-RESEARCH.md, docs/framework/migration-intake-template.md</read_first>
  <action>Create `docs/framework/schemas/migration-manifest.schema.json` as Draft 2020-12 JSON with `$schema`, `title`, `type: object`, required top-level sections `migration`, `source`, `target`, `model_spec`, `framework_adapter`, `backend_matrix`, `data_contract`, `recipe`, `validation`, `optimization`, `ownership`, and `evidence`. Add definitions or reusable object shapes for `owner_layer`, `evidence_ref`, `capability_status`, `runtime_blocker`, and `validation_target`. The `capability_status` enum must be exactly `unsupported`, `emulated`, `native`, `optimized`.</action>
  <verify>python3 -m json.tool docs/framework/schemas/migration-manifest.schema.json >/dev/null && rg '"\\$schema"|"model_spec"|"backend_matrix"|"owner_layer"|"unsupported"|"emulated"|"native"|"optimized"' docs/framework/schemas/migration-manifest.schema.json</verify>
  <acceptance_criteria>
    - JSON parses with `python3 -m json.tool`.
    - Schema has all required top-level manifest sections.
    - Schema contains the four capability maturity enum values and does not add `blocked` to that enum.
    - Schema contains separate fields for evidence or blockers.
  </acceptance_criteria>
  <done>Manifest schema skeleton exists and is syntactically valid.</done>
</task>

<task type="auto">
  <name>Task 2: Write manifest specification</name>
  <files>docs/framework/migration-manifest-spec.md</files>
  <read_first>docs/framework/schemas/migration-manifest.schema.json, docs/framework/migration-intake-template.md, .planning/phases/aimf-02-core-migration-architecture/02-CONTEXT.md</read_first>
  <action>Create `docs/framework/migration-manifest-spec.md` with sections: Purpose, Top-Level Manifest, Owner Layers, ModelSpec, FrameworkAdapter, BackendAdapter/Backend Matrix, Data Contract, Recipe, Validation, Optimization, Ownership And Evidence, Capability Status Semantics, Runtime Blockers, MiniMax M3 Example Mapping, and Review Checklist. Explicitly define `ModelSpec`, `FrameworkAdapter`, `BackendAdapter`, `DataAdapter`, `Recipe`, `ValidationSuite`, and `OptimizationLoop` responsibilities. State that advertised limits and validation targets are separate fields.</action>
  <verify>rg "ModelSpec|FrameworkAdapter|BackendAdapter|DataAdapter|Recipe|ValidationSuite|OptimizationLoop|Owner Layers|Runtime Blockers|MiniMax M3|advertised|validation target" docs/framework/migration-manifest-spec.md</verify>
  <acceptance_criteria>
    - Spec names all seven architecture contracts from ARCH-01.
    - Spec explains source/target framework, model family, backend, dataset/checkpoint format, and precision policy fields from ARCH-02.
    - Spec documents owner and evidence requirements.
    - Spec uses MiniMax M3 examples for MSA, 1M context, multimodality, checkpoint scale, and NPU runtime blockers.
  </acceptance_criteria>
  <done>Manifest specification is reviewable and grounded in the first case.</done>
</task>

<task type="auto">
  <name>Task 3: Create MiniMax M3 example manifest</name>
  <files>docs/framework/examples/veomni-minimax-m3.manifest.yaml</files>
  <read_first>docs/framework/migration-manifest-spec.md, docs/cases/veomni-minimax-m3/intake.md, docs/cases/veomni-minimax-m3/assumptions.md, docs/cases/veomni-minimax-m3/npu-runtime-evidence.md</read_first>
  <action>Create `docs/framework/examples/veomni-minimax-m3.manifest.yaml` with example values for the VeOmni + MiniMax M3 case. Include source `MiniMaxAI/MiniMax-M3`, target `VeOmni`, first slice `MiniMax M3 reference artifact intake to VeOmni tiny text smoke`, model fields for MSA/sparse attention, 1M advertised context, staged tiny/32K/128K/1M validation targets, native text/image/video modality, MoE scale, checkpoint/tokenizer unknowns, and backend matrix rows for GPU reference and Ascend NPU. Represent Ascend runtime blockers as blocker/evidence fields for root-only `npu-smi`, missing CANN, and missing `torch_npu`.</action>
  <verify>rg "MiniMaxAI/MiniMax-M3|VeOmni|MSA|1M|32K|128K|text|image|video|Ascend|npu-smi|CANN|torch_npu|unsupported|emulated|native|optimized" docs/framework/examples/veomni-minimax-m3.manifest.yaml</verify>
  <acceptance_criteria>
    - Example manifest names MiniMax M3 and VeOmni.
    - Example manifest keeps MSA, long context, multimodality, and checkpoint/tokenizer assumptions visible.
    - Example manifest separates capability status from runtime blockers.
    - Example manifest includes ownership and evidence references.
  </acceptance_criteria>
  <done>MiniMax M3 example manifest can guide later adapter and validation work.</done>
</task>

</tasks>

<verification>
Before declaring plan complete:
- [ ] `python3 -m json.tool docs/framework/schemas/migration-manifest.schema.json >/dev/null`
- [ ] `rg "ModelSpec|FrameworkAdapter|BackendAdapter|DataAdapter|Recipe|ValidationSuite|OptimizationLoop" docs/framework/migration-manifest-spec.md`
- [ ] `rg "MiniMaxAI/MiniMax-M3|VeOmni|MSA|1M|Ascend|CANN|torch_npu" docs/framework/examples/veomni-minimax-m3.manifest.yaml`
- [ ] Confirm no secrets, PATs, passwords, or private tokens appear in the example manifest.
</verification>

<success_criteria>
- Manifest schema exists with clear ownership and evidence fields.
- Manifest spec covers ARCH-01, ARCH-02, FLOW-01, and ACC-03.
- MiniMax M3 example values are present and traceable to Phase 1 artifacts.
</success_criteria>

<output>
After completion, create `.planning/phases/aimf-02-core-migration-architecture/aimf-02-01-SUMMARY.md`
</output>
