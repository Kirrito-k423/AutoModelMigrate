---
phase: 01
slug: veomni-minimax-m3-intake
status: verified
threats_open: 0
asvs_level: 1
created: 2026-06-16
---

# Phase 01 — Security

> Per-phase security contract: threat register, accepted risks, and audit trail.

This is a retroactive STRIDE audit for a documentation and planning phase. No executable product code, secrets, credentials, package downloads, or system-level NPU installation steps were added in Phase 01.

## Trust Boundaries

| Boundary | Description | Data Crossing |
|----------|-------------|---------------|
| Local repository to GitHub | Publication docs describe pushing this repository to `Kirrito-k423/AutoModelMigrate`. | Repository metadata and git commits; no credentials are stored. |
| Project docs to host NPU operations | Runtime docs describe root-only `npu-smi`, CANN, PTA/torch_npu, and Docker setup gates. | Host runtime commands, privileged-operation requirements, hardware evidence. |
| Project docs to external vendor downloads | Runtime docs link official HiAscend CANN download and VeOmni A2/910B Docker references. | Third-party packages/images and version matrix decisions, not downloaded in this phase. |
| Public model artifacts to migration planning | Intake docs cite MiniMax M3 public sources and Hugging Face artifacts. | Model metadata, tokenizer/checkpoint assumptions, future artifact hashes. |

## Threat Register

| Threat ID | Category | Component | Disposition | Mitigation | Status |
|-----------|----------|-----------|-------------|------------|--------|
| T-01-01 | Information Disclosure | GitHub publication runbook | mitigate | `docs/ops/github-publish.md` documents `gh auth login` and push readiness without storing tokens, PATs, cookies, or credentials. | closed |
| T-01-02 | Elevation of Privilege | Ascend NPU runtime runbook | mitigate | `docs/ops/ascend-npu-runtime.md` and `npu-runtime-evidence.md` record root-only `npu-smi` as an approved-path gate and do not instruct bypassing privilege controls. | closed |
| T-01-03 | Tampering / Supply Chain | CANN, torch_npu, Docker, and model artifacts | mitigate | Docs require official HiAscend source, version-matrix confirmation, traffic/disk budget confirmation, and future artifact pinning before downloads or installs. | closed |
| T-01-04 | Spoofing / Integrity | NPU execution readiness claims | mitigate | Docs repeatedly state current NPU execution is blocked until driver/DCMI, CANN, torch_npu, tensor smoke, and VeOmni smoke gates pass. | closed |
| T-01-05 | Denial of Service | Large CANN/image/model downloads | mitigate | Runtime docs warn not to download CANN or pull/build VeOmni A2/910B images until version and network/disk budget are confirmed. | closed |
| T-01-06 | Repudiation | Migration signoff evidence | mitigate | UAT, summaries, requirements, gap analysis, and runtime docs record validation evidence and explicit blockers, making unsupported claims auditable. | closed |

*Status: open · closed*
*Disposition: mitigate (implementation required) · accept (documented risk) · transfer (third-party)*

## Accepted Risks Log

No accepted risks.

## Security Audit Trail

| Audit Date | Threats Total | Closed | Open | Run By |
|------------|---------------|--------|------|--------|
| 2026-06-16 | 6 | 6 | 0 | Codex inline secure-phase audit |

## Evidence Reviewed

- `docs/ops/github-publish.md`
- `docs/ops/ascend-npu-runtime.md`
- `docs/cases/veomni-minimax-m3/npu-runtime-evidence.md`
- `docs/cases/veomni-minimax-m3/intake.md`
- `docs/cases/veomni-minimax-m3/assumptions.md`
- `docs/cases/veomni-minimax-m3/gap-analysis.md`
- `docs/framework/migration-intake-template.md`
- `docs/framework/gap-analysis-template.md`
- `docs/cases/veomni-minimax-m3/reusable-deltas.md`
- `.planning/phases/01-veomni-minimax-m3-intake/01-UAT.md`

## Sign-Off

- [x] All threats have a disposition (mitigate / accept / transfer)
- [x] Accepted risks documented in Accepted Risks Log
- [x] `threats_open: 0` confirmed
- [x] `status: verified` set in frontmatter

**Approval:** verified 2026-06-16
