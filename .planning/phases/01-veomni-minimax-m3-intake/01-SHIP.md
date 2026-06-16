---
phase: 01
slug: veomni-minimax-m3-intake
status: blocked_git_write_scope
prepared: 2026-06-16T04:04:56Z
target_repo: https://github.com/Kirrito-k423/AutoModelMigrate.git
current_branch: master
pre_handoff_head: 5b2052d
verification_status: passed
uat_status: complete
security_status: verified
validation_status: verified
---

# Phase 01 Ship Handoff

This file records the completed ship preflight for Phase 01 and the exact
external gate that prevents pushing or creating a pull request right now.

## Preflight Result

| Check | Result | Evidence |
|-------|--------|----------|
| Phase verification | Pass | `gsd-tools verification.status .planning/phases/01-veomni-minimax-m3-intake` returned `status: passed`. |
| UAT | Pass | `01-UAT.md` is `status: complete` with 4 passed, 0 issues. |
| Security | Pass | `01-SECURITY.md` is `status: verified` with `threats_open: 0`. |
| Validation | Pass | `01-VALIDATION.md` records `nyquist_compliant: true`. |
| Working tree | Pass | `git status --short` was empty during preflight. |
| Remote | Pass | `origin` points to `https://github.com/Kirrito-k423/AutoModelMigrate.git`. |
| Branch | Initial publication caveat | Current branch is `master`; there is no visible remote base branch yet. |
| GitHub CLI | Pass | `$HOME/.local/bin/gh`, version 2.94.0. |
| GitHub auth | Pass | `gh auth status` reports an active login for `Kirrito-k423`. |
| Target repo visibility | Pass | `gh repo view Kirrito-k423/AutoModelMigrate` can read the private repository. |
| Repository permission | Pass at API layer | GitHub API reports viewer permission `ADMIN` and repository `push: true`. |
| Git HTTPS transport | Blocked | `git push -u origin master` and `git ls-remote origin` return HTTPS 403: `Write access to repository not granted`. |
| Git SSH transport | Blocked by network | SSH to `github.com:22` times out from this host. |

## Blocking Gate

Shipping is blocked by GitHub Git-transport authorization, not by Phase 01
readiness or repository ownership.

Required external state:

1. Refresh GitHub CLI authorization with repository/content write scope. This
   command opens a browser/device-flow checkpoint:

```bash
gh auth refresh -h github.com -s repo
```

2. Verify the login and git transport:

```bash
gh auth status
git ls-remote origin
```

3. Publish the current local branch:

```bash
git push -u origin master
```

Notes:

- The repository exists and is private.
- `gh repo view` can read it with admin-level viewer permission.
- HTTPS git operations currently fail with 403 despite API access.
- SSH is not a viable fallback from this host because port 22 to GitHub times
  out.
- If browser refresh cannot grant git write, log in again with a token that has
  repository contents read/write access for `Kirrito-k423/AutoModelMigrate`.

## Publication Body Draft

Title:

```text
Phase 01: VeOmni + MiniMax M3 Intake
```

Body:

```markdown
## Summary

**Phase 01: VeOmni + MiniMax M3 Intake**
**Goal:** Produce a source-backed intake dossier, gap map, first vertical-slice
plan, GitHub publication path, and Ascend NPU runtime readiness plan for MiniMax
M3 in VeOmni.
**Status:** Verified

Phase 01 establishes the MiniMax M3 through VeOmni case as the first concrete
slice for the AI Infra Migration Framework. It captures source-backed model and
framework assumptions, maps migration gaps to reusable owner layers, records the
first safe vertical slice, and separates GitHub/NPU operational gates from model
implementation work.

## Changes

### Plan 01-01: Case Intake

- Created `docs/cases/veomni-minimax-m3/assumptions.md`.
- Created `docs/cases/veomni-minimax-m3/intake.md`.
- Captured MiniMax M3 MSA, long-context, multimodal, tokenizer, checkpoint,
  precision, inference, training, and NPU assumptions.
- Identified VeOmni extension surfaces for model construction, processor/data
  path, attention, distributed recipes, checkpoints, and Ascend support.

### Plan 01-02: Gap Analysis

- Created `docs/cases/veomni-minimax-m3/gap-analysis.md`.
- Grouped gaps by ModelSpec, FrameworkAdapter, BackendAdapter, DataAdapter,
  ValidationSuite, and OptimizationLoop.
- Selected the first vertical slice: MiniMax M3 reference artifact intake to
  VeOmni tiny text smoke.

### Plan 01-03: Reusable Templates

- Created `docs/framework/migration-intake-template.md`.
- Created `docs/framework/gap-analysis-template.md`.
- Created `docs/cases/veomni-minimax-m3/reusable-deltas.md`.
- Promoted reusable fields for source/target frameworks, backend scope, custom
  operators, context limits, modality contracts, validation, performance, owner
  layer, severity, first action, blocking status, selected slice, and non-goals.

### Plan 01-04: GitHub And Ascend NPU Readiness

- Created `docs/ops/github-publish.md`.
- Created `docs/ops/ascend-npu-runtime.md`.
- Created `docs/cases/veomni-minimax-m3/npu-runtime-evidence.md`.
- Recorded `origin`, `gh auth login`, initial push gate, root-only `npu-smi`
  behavior, missing CANN and `torch_npu` gates, HiAscend download caution, and
  VeOmni A2/910B Docker guidance.

## Requirements Addressed

- `M3-01`: MiniMax M3 architecture assumptions captured.
- `M3-02`: VeOmni extension points mapped.
- `M3-03`: First useful MiniMax M3 vertical slice identified.
- `M3-04`: Reusable framework artifacts produced.
- `ACC-04`: Ascend NPU readiness captured as a reusable skill-backed playbook.
- `OPS-01`: GitHub publication target and push readiness documented.

## Verification

- [x] Phase verification passed: 5/5 must-haves verified.
- [x] Required artifacts verified: 12/12.
- [x] Requirements coverage verified: 6/6 Phase 01 requirements satisfied.
- [x] UAT complete: 4 passed, 0 issues.
- [x] Security verified: 6 threats closed, 0 open.
- [x] GSD health/consistency checks passed, with only expected warnings for
      future phase directories not yet created.

## Key Decisions

- Use VeOmni + MiniMax M3 as the first hard case for reusable migration
  abstractions.
- Treat MiniMax Sparse Attention, 1M context, and native multimodality as
  first-class migration dimensions.
- Model GPU/NPU differences through backend capabilities, not scattered
  accelerator branches in model code.
- Keep NPU execution blocked until driver/DCMI, CANN, `torch_npu`, tensor
  smoke, VeOmni smoke, and MiniMax M3 smoke gates pass.
- Use VeOmni's upstream Ascend A2/910B Docker guide as the first container
  baseline on A2/910B.
- Keep GitHub publication ready locally, but do not claim ship until auth and
  push succeed.

## User Stories & Acceptance Criteria

- Acceptance criteria are covered by the linked requirements and verification
  evidence.

## Risks & Dependencies

- GitHub push and PR creation depend on `gh auth refresh -h github.com -s repo`
  or an equivalent token that can write repository contents over HTTPS.
- NPU execution depends on root-approved `npu-smi info`, CANN toolkit or a
  validated VeOmni A2/910B Docker route, matching PyTorch plus `torch_npu`, and
  tensor/framework smoke gates.
- CANN packages and VeOmni A2/910B images are large; confirm version matrix,
  traffic budget, and disk budget before download.

## Success Metrics & Release Criteria

- Release when automated verification and required manual checks pass.
- Publication is complete only after `git push -u origin master` succeeds and
  the remote branch is visible on GitHub.

## TDD Audit

Pre-handoff branch audit at `5b2052d`: 21 non-merge commits, all without
`gate_status:` trailers. This is informational and should be regenerated after
the final ship commit if a PR body is created by `$gsd-ship`.

gate_status: skill=0, fallback=0, exempt=0, missing=21
```

## Resume Checklist

After Git write authorization is complete:

1. Re-run `gh auth status`.
2. Re-run `git ls-remote origin`.
3. Re-run `git status --short`.
4. Push `master` with `git push -u origin master`.
5. If a PR-based flow is required after initial publication, create a feature
   branch from the remote default branch and rerun `$gsd-ship`.
