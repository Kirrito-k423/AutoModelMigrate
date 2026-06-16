---
phase: 01
slug: veomni-minimax-m3-intake
status: verified
nyquist_compliant: true
wave_0_complete: true
created: 2026-06-16
---

# Phase 01 — Validation Strategy

> Per-phase validation contract for feedback sampling during execution.

Phase 01 produced documentation, planning artifacts, and operational runbooks. No executable product code or runtime package installation was added, so validation is command-based and artifact-based rather than unit-test-framework based.

## Test Infrastructure

| Property | Value |
|----------|-------|
| **Framework** | Command-based Markdown/GSD validation |
| **Config file** | none — no code test framework in this documentation phase |
| **Quick run command** | `rg "<expected terms>" <target docs>` |
| **Full suite command** | `gsd-tools validate consistency && gsd-tools validate health && gsd-tools audit-open --json` |
| **Estimated runtime** | ~5 seconds |

## Sampling Rate

- **After every task commit:** Run the plan's `<verify>` command.
- **After every plan wave:** Run the plan-level `rg` or inspector commands recorded in the PLAN files.
- **Before `$gsd-verify-work`:** Run GSD consistency, health, artifact-open scan, and UAT.
- **Max feedback latency:** ~5 seconds for command checks, excluding network-independent GitHub auth status checks.

## Per-Task Verification Map

| Task ID | Plan | Wave | Requirement | Threat Ref | Secure Behavior | Test Type | Automated Command | File Exists | Status |
|---------|------|------|-------------|------------|-----------------|-----------|-------------------|-------------|--------|
| 01-01-01 | 01-01 | 1 | M3-01 | — | Facts separated from assumptions; no unsupported runtime claims. | command | `test -s docs/cases/veomni-minimax-m3/assumptions.md && rg "MSA|sparse|1M|multimodal|checkpoint|tokenizer" docs/cases/veomni-minimax-m3/assumptions.md` | yes | green |
| 01-01-02 | 01-01 | 1 | M3-02 | T-01-06 | VeOmni scope and gates are documented with source links. | command | `test -s docs/cases/veomni-minimax-m3/intake.md && rg "VeOmni Extension Points|Validation Gates|Performance Gates|First Vertical Slice" docs/cases/veomni-minimax-m3/intake.md` | yes | green |
| 01-04-01 | 01-04 | 1 | OPS-01 | T-01-01 | GitHub auth is required but no credential is stored. | command | `git remote get-url origin \| rg "Kirrito-k423/AutoModelMigrate" && gh --version && rg "Kirrito-k423|AutoModelMigrate|gh auth login|git push" docs/ops/github-publish.md README.md` | yes | green |
| 01-04-02 | 01-04 | 1 | ACC-04 | T-01-02,T-01-03,T-01-04,T-01-05 | Root/NPU/CANN/PTA guidance is gated and does not claim readiness. | command | `python3 $HOME/.codex/skills/ascend-npu-runtime/scripts/inspect_npu_env.py >/tmp/ascend-npu-runtime.json && rg "CANN|PTA|torch_npu|npu-smi|DCMI|root|required|HiAscend|traffic|driver-permission-or-root-required|Ascend A2 Docker|910b|Dockerfile.ascend_9.0.0_a2" docs/ops/ascend-npu-runtime.md` | yes | green |
| 01-04-03 | 01-04 | 1 | ACC-04 | T-01-04 | MiniMax M3 NPU status remains blocked until runtime gates pass. | command | `rg "MiniMax M3|VeOmni|BackendAdapter|CANN|torch_npu|blocked" docs/cases/veomni-minimax-m3/npu-runtime-evidence.md` | yes | green |
| 01-02-01 | 01-02 | 2 | M3-02 | T-01-06 | Gaps have owner layers and first actions. | command | `rg "ModelSpec|FrameworkAdapter|BackendAdapter|DataAdapter|ValidationSuite|OptimizationLoop" docs/cases/veomni-minimax-m3/gap-analysis.md` | yes | green |
| 01-02-02 | 01-02 | 2 | M3-03 | T-01-06 | First slice has blockers and non-goals before implementation. | command | `rg "Selected First Vertical Slice|Non-goals|Blocks Slice" docs/cases/veomni-minimax-m3/gap-analysis.md` | yes | green |
| 01-03-01 | 01-03 | 3 | M3-04 | T-01-06 | Reusable intake template remains generic. | command | `rg "Source Framework|Target Framework|Backend Scope|Validation Gates|Performance Gates" docs/framework/migration-intake-template.md` | yes | green |
| 01-03-02 | 01-03 | 3 | M3-04 | T-01-06 | Gap template and deltas capture reusable sparse/long-context/multimodal/accelerator fields. | command | `rg "Owner Layer|Severity|First Action" docs/framework/gap-analysis-template.md && rg "sparse|long context|multimodal|accelerator" docs/cases/veomni-minimax-m3/reusable-deltas.md` | yes | green |

## Wave 0 Requirements

Existing command-based infrastructure covers all Phase 01 requirements. No test framework install was needed because no executable application code was added.

## Manual-Only Verifications

All Phase 01 behaviors were validated through file existence, command checks, UAT, and security review. No separate manual-only checks remain.

## Audit Trail

| Date | Check | Result |
|------|-------|--------|
| 2026-06-16 | `gsd-tools validate consistency` | passed, with expected warnings for future Phase 2-5 directories not yet created |
| 2026-06-16 | `gsd-tools validate health` | healthy |
| 2026-06-16 | `gsd-tools audit-open --json` | no open items |
| 2026-06-16 | `01-UAT.md` | complete, 4 passed, 0 issues |
| 2026-06-16 | `01-SECURITY.md` | verified, threats_open: 0 |

## Validation Sign-Off

- [x] All tasks have `<verify>` commands or command-based artifact checks
- [x] Sampling continuity: no 3 consecutive tasks without automated verify
- [x] Wave 0 covers all MISSING references
- [x] No watch-mode flags
- [x] Feedback latency < 10s
- [x] `nyquist_compliant: true` set in frontmatter

**Approval:** approved 2026-06-16
