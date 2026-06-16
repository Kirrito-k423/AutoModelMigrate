# Phase 2: Core Migration Architecture - Context

**Gathered:** 2026-06-16T10:19:07Z
**Status:** Ready for planning

<domain>
## Phase Boundary

This phase defines the reusable architecture contracts for the AI Infra Migration Framework: migration manifest/domain model, adapter boundaries, backend capability matrix, and migration lifecycle/backlog taxonomy. It does not implement MiniMax M3 support yet; it turns Phase 1 evidence into contracts that later phases and the VeOmni + MiniMax M3 reference case can execute against.

</domain>

<decisions>
## Implementation Decisions

### Manifest And Domain Model
- **D-01:** Phase 2 should produce a schema-first migration manifest, backed by explanatory docs and MiniMax M3 example values. The manifest should be machine-checkable later, not only prose.
- **D-02:** The manifest must represent source framework/runtime, target framework/runtime, model family, accelerator backend, dataset/input contract, checkpoint/artifact contract, precision policy, validation gates, ownership, and evidence links.
- **D-03:** Model semantics belong in `ModelSpec`: architecture/operator requirements, modality contract, context/shape limits, checkpoint metadata, tokenizer/processor contract, dtype policy, and declared assumptions. MiniMax M3 example fields must include MSA/sparse attention, 1M advertised context, native text/image/video support, MoE scale, BF16/F32 metadata, and pinned artifact revision placeholders.
- **D-04:** The manifest should separate advertised capability from staged validation targets. For MiniMax M3, advertised 1M context is not the same as first-slice tiny, 32K, 128K, and eventual 1M gates.
- **D-05:** Each manifest section needs an owner layer and evidence pointer so future migrations can be planned as targeted adapter work instead of a broad rescue task.

### Adapter And Capability Boundaries
- **D-06:** Keep the Phase 1 owner layers as stable architecture contracts: `ModelSpec`, `FrameworkAdapter`, `BackendAdapter`, `DataAdapter`, `Recipe`, `ValidationSuite`, and `OptimizationLoop`.
- **D-07:** `FrameworkAdapter` owns target framework registration, model/config builders, tokenizer/processor hooks, data builders, checkpoint integration, recipes, and distributed execution hooks. For the first case, this means VeOmni extension points and any Transformers remote-code policy are framework concerns.
- **D-08:** `BackendAdapter` owns accelerator facts: device/runtime gates, precision modes, communication primitives, memory/shape constraints, graph/compile constraints, operator/kernel availability, and profiler hooks. GPU/NPU differences must not be scattered through model code.
- **D-09:** `DataAdapter` owns tokenizer, processor, dataset schema, media transforms, checkpoint file/index metadata, fixture generation, and reference outputs. This prevents MiniMax M3 tokenizer/checkpoint drift from being treated as framework logic.
- **D-10:** Capability maturity states should be `unsupported`, `emulated`, `native`, and `optimized`. Runtime readiness blockers such as Ascend root-only `npu-smi`, missing CANN, or missing `torch_npu` should be recorded as evidence/blocker fields, not as replacement maturity states.
- **D-11:** Sparse/custom operators such as MiniMax Sparse Attention must be modeled as named capabilities with backend-specific maturity and evidence. Dense fallback can be marked `emulated` only if correctness gates prove it and performance caveats are explicit.

### Migration Lifecycle And Backlog Taxonomy
- **D-12:** Migration status should follow the required lifecycle: `Intake`, `Gap Analysis`, `Adapter Build`, `Correctness`, `Scale`, `Optimize`, `Accuracy Signoff`, and `Production Ready`.
- **D-13:** Status transitions are evidence-gated. A migration cannot advance from `Correctness` to `Optimize` until smoke/parity gates exist; it cannot reach `Production Ready` without accuracy, performance, backend, and documentation signoff.
- **D-14:** Backlog items should carry owner layer, severity, evidence, first action, blocking status, dependencies, backend scope, and acceptance gate. This preserves the useful structure from the MiniMax M3 gap analysis.
- **D-15:** Backend readiness can be independently blocked while model/framework work continues on a reference path. For the current host, Ascend NPU remains blocked on root-level device evidence, CANN, PyTorch/PTA, tensor smoke, and VeOmni smoke, but this does not block Phase 2 architecture or a future CPU/GPU/reference MiniMax M3 slice.
- **D-16:** Optimization work must be downstream of correctness and accuracy contracts. `OptimizationLoop` records baseline, profiler evidence, hypothesis, config diff, before/after metrics, regression guard, and rollback criteria.

### Codex's Discretion
- Exact schema file format can be selected during planning, but it should be YAML/JSON Schema-friendly and easy to read in code review.
- Phase 2 may ship as docs plus schema/examples first; Python package code can wait unless the plan finds a low-risk, high-value scaffold.
- Naming can be adjusted during implementation if local conventions emerge, but the owner-layer boundaries above should remain stable.

</decisions>

<canonical_refs>
## Canonical References

**Downstream agents MUST read these before planning or implementing.**

### Project Scope And Requirements
- `.planning/PROJECT.md` - Project identity, constraints, and architecture direction.
- `.planning/REQUIREMENTS.md` - Requirement IDs for ARCH, FLOW, ACC, validation, and traceability.
- `.planning/ROADMAP.md` - Phase 2 boundary, success criteria, and plan list.
- `.planning/STATE.md` - Current shipped state, repository transport, NPU blockers, and continuity notes.

### Prior Phase Decisions
- `.planning/phases/01-veomni-minimax-m3-intake/01-CONTEXT.md` - Locked Phase 1 decisions and specific operational constraints.
- `docs/cases/veomni-minimax-m3/intake.md` - First-case source/target, backend, validation, and performance gates.
- `docs/cases/veomni-minimax-m3/assumptions.md` - MiniMax M3 fact/assumption map and risks.
- `docs/cases/veomni-minimax-m3/gap-analysis.md` - Owner-layer gaps, selected first vertical slice, blocking gaps, and non-goals.
- `docs/cases/veomni-minimax-m3/reusable-deltas.md` - Phase 2 architecture inputs promoted from the first case.
- `docs/cases/veomni-minimax-m3/npu-runtime-evidence.md` - Case-specific NPU runtime blockers and BackendAdapter implications.

### Templates And Operations
- `docs/framework/migration-intake-template.md` - Existing reusable intake fields that Phase 2 should formalize.
- `docs/framework/gap-analysis-template.md` - Existing owner-layer backlog structure to preserve in lifecycle taxonomy.
- `docs/ops/ascend-npu-runtime.md` - Ascend runtime gates and NPU evidence model.
- `docs/ops/github-publish.md` - Repository transport and future PR/shipping guidance.
- `.planning/notes/2026-06-16-npu-smi-root-cann-gh.md` - User-provided environment constraints for root-only `npu-smi`, CANN downloads, and `gh`.
- `.planning/notes/2026-06-16-veomni-a2-910b-docker-guide.md` - User-provided VeOmni A2/910B Docker reference note.

### External References
- `https://github.com/ByteDance-Seed/VeOmni` - VeOmni source and extension points.
- `https://veomni.readthedocs.io/en/latest/` - VeOmni documentation.
- `https://github.com/ByteDance-Seed/VeOmni/blob/main/docs/hardware_support/AscendDockerUsage/build_a2_docker.md` - Preferred Ascend A2/910B Docker starting point.
- `https://github.com/MiniMax-AI/MiniMax-M3` - MiniMax M3 public repository.
- `https://huggingface.co/MiniMaxAI/MiniMax-M3` - MiniMax M3 artifact source.
- `https://www.hiascend.com/developer/download/community/result` - Official CANN download source; large packages require version and traffic confirmation.

</canonical_refs>

<code_context>
## Existing Code Insights

### Reusable Assets
- `docs/framework/migration-intake-template.md`: already has the right broad sections for a manifest; Phase 2 should convert this into explicit contract/schema fields.
- `docs/framework/gap-analysis-template.md`: already defines owner layers and backlog fields; Phase 2 should preserve this taxonomy and make it lifecycle-aware.
- `docs/cases/veomni-minimax-m3/reusable-deltas.md`: contains direct architecture inputs for manifest fields, adapter boundaries, BackendAdapter capability states, validation states, and OptimizationLoop evidence.
- `docs/ops/ascend-npu-runtime.md`: provides backend runtime gates that should become BackendAdapter evidence fields.

### Established Patterns
- Planning artifacts are Markdown-first, source-backed, and GSD-tracked.
- Phase 1 separated case-specific docs from reusable templates; Phase 2 should keep that split.
- Current repo has no implementation package yet, so Phase 2 should avoid inventing runtime code before contracts stabilize.

### Integration Points
- Future schema/spec files should live under `docs/framework/` unless the Phase 2 plan intentionally creates a package scaffold.
- Future case examples should reference MiniMax M3 through `docs/cases/veomni-minimax-m3/`.
- State changes should use GSD tools, while documentation/spec edits can be committed as phase artifacts.

</code_context>

<specifics>
## Specific Ideas

- The framework should make a new migration mostly configuration plus targeted adapters: manifest first, gaps second, implementation third.
- MiniMax M3 must stay visible in Phase 2 as the proving example, especially MSA, long context, multimodality, checkpoint scale, precision, and NPU runtime blockers.
- Ascend NPU support should be represented as a backend capability/evidence path. The current host's root-only `npu-smi`, missing CANN, and missing `torch_npu` are architecture inputs, not reasons to fork the model adapter.
- VeOmni's upstream A2/910B Docker guide is the preferred source for NPU container planning; do not create a custom container recipe before comparing against it.

</specifics>

<deferred>
## Deferred Ideas

- Full validation harness design belongs to Phase 3.
- Detailed profiling/report schema and optimization loop execution belong to Phase 4.
- Packaging templates for future migration cases belongs to Phase 5.
- Dashboarding for migration portfolio status remains deferred to v2.

</deferred>

---

*Phase: 02-Core Migration Architecture*
*Context gathered: 2026-06-16T10:19:07Z*
