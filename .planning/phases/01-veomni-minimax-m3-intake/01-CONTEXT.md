# Phase 1: VeOmni + MiniMax M3 Intake - Context

**Gathered:** 2026-06-15
**Status:** Ready for planning

<domain>
## Phase Boundary

This phase does not implement VeOmni support yet. It establishes the first case dossier: what MiniMax M3 requires, where VeOmni can accept the integration, what gaps likely exist, and what the smallest useful vertical slice should prove.
</domain>

<decisions>
## Implementation Decisions

### Framework intent
- **D-01:** Design for recurring AI Infra migration work: algorithms, models, datasets, training features, inference features, GPU/NPU portability, performance tuning, and accuracy signoff.
- **D-02:** Use VeOmni + MiniMax M3 as the first vertical slice and force reusable abstractions to emerge from this case.
- **D-03:** Treat "migration complete" as correctness + accuracy + performance evidence, not only runnable code.

### Architecture direction
- **D-04:** Model the framework as layered contracts: ModelSpec, FrameworkAdapter, BackendAdapter, DataAdapter, Recipe, ValidationSuite, OptimizationLoop.
- **D-05:** GPU/NPU differences should live in backend capability descriptors and optimization reports, not scattered conditionals.
- **D-06:** Sparse attention, long context, multimodal inputs, checkpoint formats, and distributed recipes are first-class dimensions.

### Operations and runtime direction
- **D-07:** Publish this repository under GitHub owner `Kirrito-k423` with repository name `AutoModelMigrate`; the local `origin` remote should target `https://github.com/Kirrito-k423/AutoModelMigrate.git`.
- **D-08:** Treat Ascend NPU runtime readiness as a reusable project capability: CANN, PTA/torch_npu, `npu-smi`/DCMI, environment activation, and verification gates must be captured in a Codex skill and reflected in project evidence.

### Claude's Discretion
- Exact names of framework artifacts and schemas can evolve during Phase 2.
- The first vertical slice can be training-first or inference-first if Phase 1 evidence shows one is safer.
- Research can use public docs and repos, but must mark unverified assumptions.
</decisions>

<specifics>
## Specific Ideas

- The framework should cover "different framework migration" and "NPU/GPU migration" as one operating model.
- The workflow should naturally continue into performance optimization and accuracy validation after feature migration.
- MiniMax M3 through VeOmni is the first case, not a side example.
- Current execution environment is an Ascend NPU host. Initial scan on 2026-06-16 found `/usr/local/Ascend/driver` version `25.5.2`, but `npu-smi info` fails with `dcmi module initialize failed. ret is -8005`; `/usr/local/Ascend/ascend-toolkit` and Python `torch`/`torch_npu` are absent.
- The global Codex skill `ascend-npu-runtime` exists at `$HOME/.codex/skills/ascend-npu-runtime` and should be used to accumulate CANN/PTA/torch_npu setup checks.
</specifics>

<canonical_refs>
## Canonical References

### Project artifacts
- `.planning/PROJECT.md` - Project identity, core value, constraints, key decisions.
- `.planning/REQUIREMENTS.md` - Requirement IDs and traceability.
- `.planning/ROADMAP.md` - Phase boundary and plan list.

### External sources to consult during planning
- `https://github.com/ByteDance-Seed/VeOmni` - VeOmni source repository and extension points.
- `https://veomni.readthedocs.io/en/latest/` - VeOmni documentation.
- `https://www.minimax.io/blog/minimax-m3` - MiniMax M3 release details.
- `https://github.com/MiniMax-AI/MiniMax-M3` - MiniMax M3 public repository if available.
- `https://ascend.github.io/docs/sources/pytorch/install.html` - Ascend PyTorch/CANN installation guidance.
- `https://github.com/Ascend/pytorch` - Ascend PyTorch adapter (`torch_npu`) repository.
- `https://docs.vllm.ai/projects/ascend/en/v0.7.1/installation.html` - Example NPU runtime/container verification guidance.
</canonical_refs>

<code_context>
## Existing Code Insights

### Reusable Assets
- No implementation code exists yet in this workspace.
- GSD Core source is available at `$HOME/.codex/gsd-core` for workflow references.

### Established Patterns
- Planning artifacts follow GSD canonical `.planning` structure.

### Integration Points
- Future code should likely live under a framework package with docs/spec templates, not directly in `.planning`.
</code_context>

<deferred>
## Deferred Ideas

- Dashboarding for migration portfolio status.
- Deep vendor kernel implementation.
- Multi-model generalization beyond MiniMax M3 before the first case is understood.
</deferred>

---
*Phase: 01-veomni-minimax-m3-intake*
*Context gathered: 2026-06-15*
