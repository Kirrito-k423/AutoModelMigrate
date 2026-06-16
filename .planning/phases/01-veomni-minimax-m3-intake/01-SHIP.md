---
phase: 01
slug: veomni-minimax-m3-intake
status: blocked_auth
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
| GitHub auth | Blocked | `gh auth status` reports no authenticated GitHub hosts. |
| Target repo visibility | Blocked | GitHub connector lookup for `Kirrito-k423/AutoModelMigrate` returned 404 Not Found. |

## Blocking Gate

Shipping is blocked by GitHub authentication and repository visibility, not by
Phase 01 readiness.

Required external state:

1. Authenticate GitHub CLI:

```bash
gh auth login
gh auth status
```

2. Ensure `Kirrito-k423/AutoModelMigrate` exists and the authenticated account
   can push to it. If it does not exist and the account can create it:

```bash
gh repo create Kirrito-k423/AutoModelMigrate --private --source=. --remote=origin
```

Use `--public` only if the publication policy explicitly allows it.

3. Publish the current local branch as the initial remote branch:

```bash
git push -u origin master
```

Because this is the initial publication and the target repository is not visible
yet, this first ship is likely an initial branch push rather than a normal PR
from a feature branch. After the repository has a real default branch, later
phases should ship from feature branches and PRs.

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

- GitHub push and PR creation depend on `gh auth login` and repository
  visibility under `Kirrito-k423/AutoModelMigrate`.
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

After authentication is complete:

1. Re-run `gh auth status`.
2. Re-run `git status --short`.
3. Confirm or create `Kirrito-k423/AutoModelMigrate`.
4. Push `master` with `git push -u origin master`.
5. If a PR-based flow is required after initial publication, create a feature
   branch from the remote default branch and rerun `$gsd-ship`.

