---
phase: 03
slug: correctness-and-accuracy-harness
status: draft
nyquist_compliant: true
wave_0_complete: true
created: 2026-06-16
---

# Phase 03 - Validation Strategy

Per-phase validation contract for feedback sampling during execution.

## Test Infrastructure

| Property | Value |
|----------|-------|
| Framework | Shell assertions, `python3 -m json.tool`, and Python YAML parsing |
| Config file | None - docs/schema phase |
| Quick run command | `test -f docs/framework/correctness-validation-harness.md && test -f docs/framework/accuracy-drift-signoff.md` |
| Full suite command | `python3 -m json.tool docs/framework/schemas/validation-recipe.schema.json >/dev/null && python3 -m json.tool docs/framework/schemas/accuracy-signoff.schema.json >/dev/null && rg "Smoke|Parity|Accuracy|Drift|blocks_transition" docs/framework` |
| Estimated runtime | ~5 seconds |

## Sampling Rate

- After every task commit: run the verification command listed for that task.
- After each plan: run the full suite command above.
- Before verify-work: full suite must be green and both example YAML files must parse.
- Max feedback latency: 10 seconds.

## Per-Task Verification Map

| Task ID | Plan | Requirement | Threat Ref | Secure Behavior | Test Type | Automated Command | Status |
|---------|------|-------------|------------|-----------------|-----------|-------------------|--------|
| 03-01-01 | 03-01 | FLOW-03, VAL-01 | T-03-01 | Prevents "runs once" from becoming correctness signoff | source | `rg "manifest|tokenizer|checkpoint|forward|loss|deterministic|backend runtime" docs/framework/correctness-validation-harness.md` | pending |
| 03-01-02 | 03-01 | FLOW-03, VAL-01 | T-03-02 | Makes validation recipes reproducible and manifest-addressable | schema | `python3 -m json.tool docs/framework/schemas/validation-recipe.schema.json >/dev/null` | pending |
| 03-01-03 | 03-01 | FLOW-03, VAL-01 | T-03-03 | Keeps MiniMax M3 first slice small while preserving deferred gates | source/yaml | `python3 - <<'PY'\nimport yaml\nwith open('docs/framework/examples/veomni-minimax-m3.validation-recipe.yaml', encoding='utf-8') as f:\n    yaml.safe_load(f)\nPY` | pending |
| 03-02-01 | 03-02 | VAL-02 | T-03-04 | Separates accuracy signoff from smoke/parity | source | `rg "baseline|dataset|metric|threshold|dtype|backend|reviewer" docs/framework/accuracy-drift-signoff.md` | pending |
| 03-02-02 | 03-02 | VAL-02 | T-03-05 | Requires versioned thresholds and evidence links | schema | `python3 -m json.tool docs/framework/schemas/accuracy-signoff.schema.json >/dev/null` | pending |
| 03-02-03 | 03-02 | VAL-01, VAL-02 | T-03-06 | Allows validation results to block lifecycle transitions | source/yaml | `rg "blocks_transition|Correctness|Accuracy Signoff|Production Ready" docs/framework/validation-result-lifecycle.md docs/framework/examples/veomni-minimax-m3.accuracy-signoff.yaml` | pending |

## Wave 0 Requirements

Existing infrastructure covers this phase: shell, `rg`, Python 3, and PyYAML are available. No additional packages are required.

## Manual-Only Verifications

All Phase 3 artifacts have source, schema, or YAML parsing checks. Human review is still useful for threshold reasonableness, but the phase should not depend on manual inspection alone.

## Validation Sign-Off

- [x] All tasks have automated verify commands or source assertions.
- [x] Sampling continuity: no 3 consecutive tasks without automated verify.
- [x] Wave 0 covers all missing infrastructure references.
- [x] No watch-mode flags.
- [x] Feedback latency < 10s.
- [x] `nyquist_compliant: true` set in frontmatter.

**Approval:** approved 2026-06-16 for planning use
