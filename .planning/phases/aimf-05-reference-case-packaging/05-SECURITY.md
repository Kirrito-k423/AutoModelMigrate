---
phase: aimf-05-reference-case-packaging
slug: reference-case-packaging
status: verified
threats_open: 0
asvs_level: 1
created: 2026-06-16
updated: 2026-06-16
---

# Phase 05 Security

Per-phase security contract: threat register, accepted risks, and audit trail.

## Trust Boundaries

| Boundary | Description | Data Crossing |
|----------|-------------|---------------|
| Generic framework docs | `docs/framework/` must stay model-neutral and reusable. | Template fields, schemas, guide text, and lifecycle instructions. |
| Case evidence docs | `docs/cases/veomni-minimax-m3/` may contain MiniMax M3, VeOmni, Ascend NPU, MSA, 1M context, multimodal, and host-runtime facts. | Case facts, blockers, assumptions, and support status. |
| Runtime evidence | Runtime readiness must be represented as backend evidence and capability status. | Device/runtime/toolkit/package evidence and blockers. |
| Support claims | Documentation can describe examples, but support claims require validation, accuracy, performance, and runtime evidence. | Reader-facing status and handoff claims. |

## Threat Register

| Threat ID | Category | Component | Disposition | Mitigation | Status |
|-----------|----------|-----------|-------------|------------|--------|
| T-05-01-01 | Integrity | Schema-backed templates | mitigate | Templates link to canonical specs/schemas/examples and avoid redefining machine contracts; YAML parse checks passed. | closed |
| T-05-01-02 | Availability | Existing intake and gap template paths | mitigate | Existing `migration-intake-template.md` and `gap-analysis-template.md` were preserved and linked from `template-pack.md` and README. | closed |
| T-05-01-03 | Integrity | Generic templates | mitigate | Generic template files avoid MiniMax M3, VeOmni, MSA, 1M, Ascend A2, and 910B defaults; case facts remain in examples/case docs. | closed |
| T-05-02-01 | Integrity | Next-case guide | mitigate | `next-case-guide.md` is structured around template order, evidence gates, first slice, runtime blockers, and ready-to-plan checklist rather than MiniMax recap. | closed |
| T-05-02-02 | Spoofing | Reference-case support status | mitigate | `reference-case.md` states the package is not a support claim and marks Ascend NPU/runtime status as blocked pending evidence. | closed |
| T-05-02-03 | Integrity | Runtime blocker routing | mitigate | `next-case-guide.md`, `reference-case.md`, and reusable deltas route runtime blockers through backend runtime evidence, capability rows, and backlog/handoff records. | closed |

## Accepted Risks Log

No accepted risks.

## Audit Evidence

| Check | Evidence |
|-------|----------|
| Template field integrity | `python3` YAML parse check passed for manifest, validation recipe, accuracy signoff, and performance profile templates. |
| Generic/case separation | `! rg "MiniMax M3|VeOmni|MSA|1M|Ascend A2|910B" docs/framework/*template* docs/framework/template-pack.md` passed. |
| Next-case guide behavior | `rg "ready to plan|first slice|runtime blocker|correctness-before-optimization" docs/framework/next-case-guide.md` passed. |
| Reference support boundary | `rg "not a support claim|case evidence|generic contract|blocked" docs/cases/veomni-minimax-m3/reference-case.md` passed. |
| README discoverability | `rg "next-case-guide|reference-case|worked example|runtime evidence" docs/framework/README.md` passed. |
| Phase verification | `.planning/phases/aimf-05-reference-case-packaging/05-VERIFICATION.md` records phase checks passing. |
| UAT | `.planning/phases/aimf-05-reference-case-packaging/05-UAT.md` records 5 passed tests and 0 issues. |

## Security Audit Trail

| Audit Date | Threats Total | Closed | Open | Run By |
|------------|---------------|--------|------|--------|
| 2026-06-16 | 6 | 6 | 0 | Codex |

## Sign-Off

- [x] All threats have a disposition.
- [x] Accepted risks documented in Accepted Risks Log.
- [x] `threats_open: 0` confirmed.
- [x] `status: verified` set in frontmatter.

Approval: verified 2026-06-16.
