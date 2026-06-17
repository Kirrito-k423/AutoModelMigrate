---
phase: aimf-02
slug: core-migration-architecture
status: verified
nyquist_compliant: true
wave_0_complete: true
created: 2026-06-16
---

# Phase aimf-02 — Validation Strategy

> Per-phase validation contract for feedback sampling during execution.

---

## Test Infrastructure

| Property | Value |
|----------|-------|
| **Framework** | Shell assertions plus `python3 -m json.tool` for JSON syntax |
| **Config file** | None — docs/schema phase |
| **Quick run command** | `test -f docs/framework/migration-manifest-spec.md && test -f docs/framework/adapter-contracts.md && test -f docs/framework/migration-lifecycle.md` |
| **Full suite command** | `python3 -m json.tool docs/framework/schemas/migration-manifest.schema.json >/dev/null && rg "unsupported|emulated|native|optimized" docs/framework/backend-capability-matrix.md && rg "Intake|Gap Analysis|Adapter Build|Correctness|Scale|Optimize|Accuracy Signoff|Production Ready" docs/framework/migration-lifecycle.md` |
| **Estimated runtime** | ~5 seconds |

---

## Sampling Rate

- **After every task commit:** Run the quick existence/assertion command relevant to the touched artifact.
- **After every plan wave:** Run the full suite command above.
- **Before `$gsd-verify-work`:** Full suite must be green.
- **Max feedback latency:** 10 seconds.

---

## Per-Task Verification Map

| Task ID | Plan | Wave | Requirement | Threat Ref | Secure Behavior | Test Type | Automated Command | File Exists | Status |
|---------|------|------|-------------|------------|-----------------|-----------|-------------------|-------------|--------|
| aimf-02-01-01 | 01 | 1 | ARCH-01, ARCH-02, FLOW-01 | T-02-01 / T-02-02 | No secrets in example manifest; ownership/evidence fields prevent unverifiable claims | source/schema | `python3 -m json.tool docs/framework/schemas/migration-manifest.schema.json >/dev/null` | yes | green |
| aimf-02-01-02 | 01 | 1 | ARCH-02, ACC-03 | T-02-02 | MiniMax M3 example records assumptions and blockers instead of false support claims | source | `rg "MiniMax M3|MSA|1M|text|image|video|evidence" docs/framework/examples/veomni-minimax-m3.manifest.yaml` | yes | green |
| aimf-02-02-01 | 02 | 1 | ARCH-01, ARCH-03, ACC-01, ACC-02 | T-02-03 | Backend-specific behavior is isolated behind BackendAdapter descriptors | source | `rg "ModelSpec|FrameworkAdapter|BackendAdapter|DataAdapter|ValidationSuite|OptimizationLoop" docs/framework/adapter-contracts.md` | yes | green |
| aimf-02-02-02 | 02 | 1 | ARCH-04, ACC-02, ACC-03 | T-02-04 | Capability matrix separates maturity states from runtime blockers | source | `rg "unsupported|emulated|native|optimized|runtime blocker|CANN|torch_npu" docs/framework/backend-capability-matrix.md` | yes | green |
| aimf-02-03-01 | 03 | 2 | FLOW-02, FLOW-04 | T-02-05 | Lifecycle transitions require evidence before signoff | source | `rg "Intake|Gap Analysis|Adapter Build|Correctness|Scale|Optimize|Accuracy Signoff|Production Ready" docs/framework/migration-lifecycle.md` | yes | green |
| aimf-02-03-02 | 03 | 2 | FLOW-02, ARCH-04 | T-02-05 | Backlog taxonomy preserves owner layer, severity, evidence, first action, and blocking status | source | `rg "owner_layer|severity|evidence|first_action|blocks_slice|acceptance_gate" docs/framework/backlog-taxonomy.md` | yes | green |

*Status: pending | green | red | flaky*

---

## Wave 0 Requirements

Existing infrastructure covers this phase: shell, `rg`, and Python 3 are available. No test framework installation is required.

---

## Manual-Only Verifications

All phase behaviors have automated source or syntax checks. Human review is still useful for architecture quality, but it is not the only verification mechanism.

---

## Validation Sign-Off

- [x] All tasks have automated verify commands or source assertions.
- [x] Sampling continuity: no 3 consecutive tasks without automated verify.
- [x] Wave 0 covers all missing infrastructure references.
- [x] No watch-mode flags.
- [x] Feedback latency < 10s.
- [x] `nyquist_compliant: true` set in frontmatter.

**Approval:** approved 2026-06-16 for planning use

## Validation Audit 2026-06-17

| Metric | Count |
|--------|-------|
| Gaps found | 0 |
| Resolved | 6 |
| Escalated | 0 |

Phase 02 remains Nyquist-compliant for a documentation/schema phase. The listed
source and schema commands are executable, fast, and were rechecked during
milestone closeout.
