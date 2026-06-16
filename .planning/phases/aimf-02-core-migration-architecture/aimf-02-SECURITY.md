---
phase: aimf-02
slug: core-migration-architecture
status: verified
threats_open: 0
asvs_level: 1
created: 2026-06-16
---

# Phase aimf-02 - Security

Per-phase security contract: threat register, accepted risks, and audit trail.

This is a retroactive STRIDE audit for a documentation, schema, and architecture-contract phase. No executable product code, credentials, dependency installation, CANN download, model download, or privileged NPU operation was added in Phase 2.

## Trust Boundaries

| Boundary | Description | Data Crossing |
|----------|-------------|---------------|
| Framework docs to future migration implementers | Phase 2 defines reusable contracts that future phases will follow. | Migration status, backend capability, validation, and optimization requirements. |
| Project docs to Ascend host operations | Contracts reference NPU runtime blockers and setup gates. | Root-only device visibility, CANN/PTA setup, `torch_npu`, and VeOmni smoke evidence. |
| Project docs to external sources | Examples link MiniMax M3, Hugging Face, HiAscend, and VeOmni upstream docs. | Public URLs, pending artifact pins, future package and model downloads. |
| Example manifests to support claims | Example values may be mistaken for completed support. | Capability maturity states, blockers, and evidence references. |

## Threat Register

| Threat ID | Category | Component | Disposition | Mitigation | Status |
|-----------|----------|-----------|-------------|------------|--------|
| T-02-01 | Information Disclosure | Manifest examples and framework docs | mitigate | Secret-pattern scan found no PATs, private keys, password fields, API keys, or secret fields; examples use public URLs and project-doc evidence only. | closed |
| T-02-02 | Spoofing / Integrity | Backend support claims | mitigate | Capability docs keep `blocked` out of maturity state, require evidence per row, and mark MiniMax M3 MSA and Ascend NPU paths as `unsupported` until gates pass. | closed |
| T-02-03 | Tampering / Supply Chain | CANN, PTA/torch_npu, VeOmni Docker, MiniMax artifacts | mitigate | Docs require official HiAscend source, version-matrix confirmation, traffic/disk confirmation, upstream VeOmni A2/910B Docker guidance, and future artifact pinning before install or execution claims. | closed |
| T-02-04 | Elevation of Privilege | Root-only `npu-smi` evidence | mitigate | Runtime blockers require root-approved `npu-smi info` evidence and do not describe bypassing host privilege controls. | closed |
| T-02-05 | Denial of Service | Large downloads and long-context runs | mitigate | Manifest non-goals and runtime docs block CANN/image/model downloads until budget confirmation and stage context validation from tiny to 1M. | closed |
| T-02-06 | Repudiation | Migration lifecycle and backlog status | mitigate | Lifecycle transitions, backlog taxonomy, validation targets, and optimization records require evidence links before support or performance status can advance. | closed |

*Status: open | closed*
*Disposition: mitigate (implementation required) | accept (documented risk) | transfer (third-party)*

## Accepted Risks Log

No accepted risks.

## Security Audit Trail

| Audit Date | Threats Total | Closed | Open | Run By |
|------------|---------------|--------|------|--------|
| 2026-06-16 | 6 | 6 | 0 | Codex inline secure-phase audit |

## Evidence Reviewed

- `docs/framework/schemas/migration-manifest.schema.json`
- `docs/framework/migration-manifest-spec.md`
- `docs/framework/examples/veomni-minimax-m3.manifest.yaml`
- `docs/framework/adapter-contracts.md`
- `docs/framework/backend-capability-matrix.md`
- `docs/framework/examples/veomni-minimax-m3-capabilities.md`
- `docs/framework/migration-lifecycle.md`
- `docs/framework/backlog-taxonomy.md`
- `docs/framework/README.md`
- `docs/ops/ascend-npu-runtime.md`
- `docs/cases/veomni-minimax-m3/npu-runtime-evidence.md`

## Sign-Off

- [x] All threats have a disposition (mitigate / accept / transfer)
- [x] Accepted risks documented in Accepted Risks Log
- [x] `threats_open: 0` confirmed
- [x] `status: verified` set in frontmatter

**Approval:** verified 2026-06-16
