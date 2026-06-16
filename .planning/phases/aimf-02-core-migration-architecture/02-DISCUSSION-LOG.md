# Phase 2: Core Migration Architecture - Discussion Log

> **Audit trail only.** Do not use as input to planning, research, or execution agents.
> Decisions are captured in CONTEXT.md — this log preserves the alternatives considered.

**Date:** 2026-06-16T10:19:07Z
**Phase:** 2-Core Migration Architecture
**Areas discussed:** Manifest and domain model, Adapter and capability boundaries, Migration lifecycle and backlog taxonomy

---

## Manifest and domain model

| Option | Description | Selected |
|--------|-------------|----------|
| Schema-first manifest | Define a machine-checkable manifest with docs and MiniMax M3 example values. | ✓ |
| Prose-only architecture note | Keep Phase 2 as a narrative document and defer schema details. | |
| Implementation-first Python objects | Start from runtime classes and derive docs later. | |

**User's choice:** Auto-selected recommended default under `$gsd-discuss-phase 2 --auto`.
**Notes:** Phase 1 already produced intake/gap templates and MiniMax M3 reusable deltas, so schema-first is the safest planning input. It keeps ownership, evidence, and example values reviewable before implementation.

---

## Adapter and capability boundaries

| Option | Description | Selected |
|--------|-------------|----------|
| Backend capability descriptors | Put GPU/NPU/runtime/operator differences behind BackendAdapter capability and evidence records. | ✓ |
| Framework-specific branches | Encode differences in target framework integrations. | |
| Model adapter conditionals | Put accelerator conditionals directly in model adapter logic. | |

**User's choice:** Auto-selected recommended default under `$gsd-discuss-phase 2 --auto`.
**Notes:** This preserves the project constraint that migration logic must not be tangled with one framework trainer or one accelerator runtime. MiniMax Sparse Attention should be a named capability, not a hidden dense fallback or NPU branch.

---

## Migration lifecycle and backlog taxonomy

| Option | Description | Selected |
|--------|-------------|----------|
| Evidence-gated lifecycle | Track Intake -> Gap Analysis -> Adapter Build -> Correctness -> Scale -> Optimize -> Accuracy Signoff -> Production Ready with explicit gates. | ✓ |
| Loose milestone checklist | Track progress through informal checkboxes only. | |
| Issue labels only | Use GitHub labels as the primary workflow model. | |

**User's choice:** Auto-selected recommended default under `$gsd-discuss-phase 2 --auto`.
**Notes:** The existing gap-analysis template already captures owner layer, severity, first action, and blocking status. Phase 2 should formalize that into lifecycle-aware backlog fields and prevent optimization work from starting before correctness and accuracy gates exist.

---

## Codex's Discretion

- Exact schema filename and format can be selected during planning.
- Phase 2 may remain docs/schema/examples first unless planning finds a small package scaffold with clear value.
- `blocked-runtime` should be represented as evidence/blocker metadata while capability maturity remains `unsupported`, `emulated`, `native`, or `optimized`.

## Deferred Ideas

- Full correctness and accuracy harness details belong to Phase 3.
- Detailed profiler report and optimization execution workflow belong to Phase 4.
- Future migration template packaging belongs to Phase 5.
