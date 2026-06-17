# Project Retrospective

A living document updated after each milestone. Lessons feed forward into
future planning.

## Milestone: v1.0 - milestone

**Shipped:** 2026-06-17
**Phases:** 5 | **Plans:** 13 | **Tasks:** 28

### What Was Built

- Source-backed VeOmni + MiniMax M3 intake, assumption map, gap analysis, and
  first-slice framing.
- Reusable framework contracts for manifests, adapters, backend capabilities,
  lifecycle states, backlog taxonomy, validation, accuracy, performance, and
  optimization.
- Template pack, next-case operating guide, migration handoff template, and
  MiniMax M3 reference-case index.
- GSD evidence set: verification, UAT, security, Nyquist validation, and
  milestone audit.

### What Worked

- Starting from a difficult real case prevented the framework from becoming too
  abstract.
- Keeping Ascend NPU runtime readiness as explicit blocked evidence avoided
  unsupported execution claims.
- Small documentation/schema checks with `rg`, JSON parsing, YAML parsing, UAT,
  and security reviews were enough to validate this docs-first milestone.
- PR-based phase shipping let the user merge finished branches while the local
  workflow continued.

### What Was Inefficient

- GitHub PAT permissions allowed SSH pushes but blocked automated PR creation,
  so ship handoffs required manual PR creation/merge.
- Some generated milestone tooling still expected a versioned ROADMAP shape and
  reported stale counts, requiring manual closeout review.
- Phase 2 and Phase 3 validation files had green signoff but stale `pending`
  table cells; the milestone audit caught and corrected that mismatch.

### Patterns Established

- Model/framework-specific facts live under `docs/cases/`; generic reusable
  contracts live under `docs/framework/`.
- Backend support is represented as capability evidence and runtime blockers,
  not model forks or scattered accelerator branches.
- Correctness and accuracy gates precede performance optimization.
- Reference examples are not support claims until runtime, validation, accuracy,
  and performance evidence all exist for the declared scope.

### Key Lessons

1. Treat support-claim language as a first-class security and integrity concern.
2. Keep migration status transitions evidence-gated; a runnable smoke path is
   not the same as production readiness.
3. Archive milestone requirements before deleting the living requirements file.
4. Verify generated GSD state against current files and git history before
   trusting automated closeout counts.

### Cost Observations

- Model mix: Codex-only in this workspace.
- Sessions: multiple continuation turns across phase execution, shipping, PR
  merge follow-up, validation closeout, and milestone archival.
- Notable: PR creation permissions, not implementation complexity, were the
  dominant operational delay.

---

## Cross-Milestone Trends

### Process Evolution

| Milestone | Sessions | Phases | Key Change |
|-----------|----------|--------|------------|
| v1.0 | multiple | 5 | Established docs-first GSD migration framework with evidence gates. |

### Cumulative Quality

| Milestone | Tests | Coverage | Zero-Dep Additions |
|-----------|-------|----------|--------------------|
| v1.0 | 21 UAT checks plus schema/YAML/source assertions | 21/21 v1 requirements | 0 runtime dependencies |

### Top Lessons (Verified Across Milestones)

1. Capability matrices and backend evidence keep GPU/NPU support honest.
2. A reference case is most useful when it explicitly separates case evidence
   from generic framework contracts.
