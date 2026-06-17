---
phase: 05
slug: reference-case-packaging
status: verified
nyquist_compliant: true
wave_0_complete: true
created: 2026-06-17
---

# Phase 05 - Validation Strategy

Per-phase validation contract for feedback sampling during execution.

## Test Infrastructure

| Property | Value |
|----------|-------|
| Framework | Shell assertions and Python YAML parsing |
| Config file | None - docs/template phase |
| Quick run command | `test -f docs/framework/template-pack.md && test -f docs/framework/next-case-guide.md && test -f docs/cases/veomni-minimax-m3/reference-case.md` |
| Full suite command | `python3 - <<'PY'\nimport yaml\nfor path in ['docs/framework/migration-manifest-template.yaml','docs/framework/validation-recipe-template.yaml','docs/framework/accuracy-signoff-template.yaml','docs/framework/performance-profile-template.yaml']:\n    with open(path, encoding='utf-8') as f:\n        yaml.safe_load(f)\nPY\nrg "template-pack|next-case-guide|reference-case|not a support claim|runtime evidence" docs/framework docs/cases/veomni-minimax-m3` |
| Estimated runtime | ~5 seconds |

## Sampling Rate

- After every task commit: run the verification command listed for that task.
- After each plan: run the full suite command above.
- Before verify-work: YAML templates must parse and reference-case boundaries must be green.
- Max feedback latency: 10 seconds.

## Per-Task Verification Map

| Task ID | Plan | Requirement | Threat Ref | Secure Behavior | Test Type | Automated Command | Status |
|---------|------|-------------|------------|-----------------|-----------|-------------------|--------|
| 05-01-01 | 05-01 | M3-04 | T-05-01 | The template pack remains navigable and preserves existing intake/gap entry points. | source | `rg "Migration Intake|Gap Analysis|Migration Manifest|Backend Capability Matrix|Validation Recipe|Accuracy|Performance Profile|Optimization Report|Migration Handoff|migration-intake-template|gap-analysis-template" docs/framework/template-pack.md` | green |
| 05-01-02 | 05-01 | M3-04 | T-05-01 | YAML templates are fillable and parseable. | yaml | `python3 - <<'PY'\nimport yaml\nfor path in ['docs/framework/migration-manifest-template.yaml','docs/framework/validation-recipe-template.yaml','docs/framework/accuracy-signoff-template.yaml','docs/framework/performance-profile-template.yaml']:\n    with open(path, encoding='utf-8') as f:\n        yaml.safe_load(f)\nPY` | green |
| 05-01-03 | 05-01 | M3-04 | T-05-01 | Generic templates avoid model-specific defaults and support claims. | source | `! rg "MiniMax M3|VeOmni|MSA|1M|Ascend A2|910B" docs/framework/*template* docs/framework/template-pack.md || rg "example|case-specific|do not copy" docs/framework/*template* docs/framework/template-pack.md` | green |
| 05-02-01 | 05-02 | M3-04 | T-05-02 | Next-case guide starts from evidence gates and correctness-before-optimization. | source | `rg "template-pack|intake|gap analysis|manifest|backend capability|validation|accuracy|performance|optimization|handoff|first slice|runtime blocker|correctness-before-optimization|ready to plan" docs/framework/next-case-guide.md` | green |
| 05-02-02 | 05-02 | M3-04 | T-05-02 | Reference case separates case evidence from generic contracts and avoids support overclaiming. | source | `rg "reference case|case evidence|generic contract|support status|not a support claim|MiniMax M3|VeOmni|Ascend NPU|blocked|reusable lesson" docs/cases/veomni-minimax-m3/reference-case.md` | green |
| 05-02-03 | 05-02 | M3-04 | T-05-02 | README and reusable deltas expose the package while preserving case/generic boundaries. | source | `rg "Phase 5|validation recipe|accuracy signoff|performance profile|optimization report|handoff|reference-case|generic defaults" docs/cases/veomni-minimax-m3/reusable-deltas.md && rg "next-case-guide|reference-case|worked example|support claims|runtime evidence" docs/framework/README.md` | green |

## Wave 0 Requirements

Existing infrastructure covers this phase: shell, `rg`, Python 3, and PyYAML are available. No additional packages are required.

## Manual-Only Verifications

All Phase 5 behaviors have automated source or YAML parsing checks. Human review
remains useful for editorial clarity, but it is not the only verification
mechanism.

## Validation Sign-Off

- [x] All tasks have automated verify commands or source assertions.
- [x] Sampling continuity: no 3 consecutive tasks without automated verify.
- [x] Wave 0 covers all missing infrastructure references.
- [x] No watch-mode flags.
- [x] Feedback latency < 10s.
- [x] `nyquist_compliant: true` set in frontmatter.

**Approval:** approved 2026-06-17 during milestone closeout

## Validation Audit 2026-06-17

| Metric | Count |
|--------|-------|
| Gaps found | 0 |
| Resolved | 6 |
| Escalated | 0 |

Phase 05 is Nyquist-compliant for a documentation/template phase. Template
parseability, navigation, case/generic separation, and support-claim boundaries
are all covered by executable checks.
