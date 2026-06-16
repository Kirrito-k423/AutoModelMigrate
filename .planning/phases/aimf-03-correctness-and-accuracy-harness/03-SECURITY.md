---
phase: 03
slug: correctness-and-accuracy-harness
status: verified
threats_open: 0
asvs_level: 1
created: 2026-06-16
---

# Phase 03 - Security

Per-phase security contract: threat register, accepted risks, and audit trail.

This is a retroactive STRIDE audit for a documentation and schema phase. No executable product code, credentials, dependency installation, CANN download, model download, or privileged NPU operation was added in Phase 3.

## Trust Boundaries

| Boundary | Description | Data Crossing |
|----------|-------------|---------------|
| Validation contracts to future execution | Phase 3 docs define what future validation runs must record. | Commands, fixtures, evidence refs, baseline/candidate metadata, lifecycle decisions. |
| Example artifacts to support claims | MiniMax M3 examples may be mistaken for completed validation. | Pending/blocked status, threshold placeholders, NPU blockers, accuracy signoff state. |
| Project docs to Ascend runtime | Validation recipes reference root `npu-smi`, CANN, `torch_npu`, tensor smoke, and VeOmni smoke gates. | Privileged operations and vendor runtime setup evidence. |
| Public artifacts to accuracy signoff | Accuracy signoff depends on baselines, eval sets, hashes, and metrics that are not pinned yet. | Public model/eval artifacts and future result evidence. |

## Threat Register

| Threat ID | Category | Component | Disposition | Mitigation | Status |
|-----------|----------|-----------|-------------|------------|--------|
| T-03-01 | Spoofing / Integrity | Correctness support claims | mitigate | Harness separates smoke, parity, loss, determinism, backend runtime, and transition gates; examples keep pending gates pending. | closed |
| T-03-02 | Repudiation | Validation recipe evidence | mitigate | Recipe schema requires baseline, candidate, environment, fixtures, gates, results, evidence, blocking transitions, and status. | closed |
| T-03-03 | Information Disclosure | Validation docs/examples | mitigate | Credential-pattern scan found no PATs, private keys, password fields, API keys, or secret fields. | closed |
| T-03-04 | Elevation of Privilege | Ascend NPU validation gates | mitigate | NPU validation requires root-approved evidence but does not instruct bypassing privilege controls; gates remain blocked until runtime evidence exists. | closed |
| T-03-05 | Tampering / Supply Chain | Baselines, eval sets, model artifacts | mitigate | Accuracy signoff requires pinned baseline/candidate revisions, artifact hashes, eval-set versioning, metric definitions, and reviewer evidence. | closed |
| T-03-06 | Denial of Service | Full checkpoint, 1M context, multimodal, NPU runs | mitigate | MiniMax M3 examples keep expensive scopes as non-goals, deferred gates, or blocked runtime status until prerequisites and budgets are declared. | closed |

*Status: open | closed*
*Disposition: mitigate (implementation required) | accept (documented risk) | transfer (third-party)*

## Accepted Risks Log

No accepted risks.

## Security Audit Trail

| Audit Date | Threats Total | Closed | Open | Run By |
|------------|---------------|--------|------|--------|
| 2026-06-16 | 6 | 6 | 0 | Codex inline secure-phase audit |

## Evidence Reviewed

- `docs/framework/correctness-validation-harness.md`
- `docs/framework/schemas/validation-recipe.schema.json`
- `docs/framework/examples/veomni-minimax-m3.validation-recipe.yaml`
- `docs/framework/accuracy-drift-signoff.md`
- `docs/framework/schemas/accuracy-signoff.schema.json`
- `docs/framework/validation-result-lifecycle.md`
- `docs/framework/examples/veomni-minimax-m3.accuracy-signoff.yaml`
- `docs/framework/README.md`
- `docs/ops/ascend-npu-runtime.md`
- `docs/cases/veomni-minimax-m3/npu-runtime-evidence.md`

## Sign-Off

- [x] All threats have a disposition (mitigate / accept / transfer)
- [x] Accepted risks documented in Accepted Risks Log
- [x] `threats_open: 0` confirmed
- [x] `status: verified` set in frontmatter

**Approval:** verified 2026-06-16
