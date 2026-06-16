---
phase: aimf-05-reference-case-packaging
status: blocked
branch: codex/phase-5-reference-case-packaging
base_branch: codex/phase-3-correctness-accuracy-harness
head_sha: 3c575398cb1103b5a4071a014e328568f6c459af
pushed: true
pr_created: false
created: 2026-06-16
---

# Phase 05 Ship Handoff

## Result

Phase 05 is complete locally and pushed to GitHub, but automatic PR creation is
blocked by the current GitHub token's branch/ref permissions.

## Branch

| Field | Value |
|-------|-------|
| Repository | `Kirrito-k423/AutoModelMigrate` |
| Branch | `codex/phase-5-reference-case-packaging` |
| Base | `codex/phase-3-correctness-accuracy-harness` |
| Head SHA | `3c575398cb1103b5a4071a014e328568f6c459af` |
| Commits over base | 18 |
| Push status | pushed to `origin/codex/phase-5-reference-case-packaging` |

Compare URL:

`https://github.com/Kirrito-k423/AutoModelMigrate/compare/codex/phase-3-correctness-accuracy-harness...codex/phase-5-reference-case-packaging?expand=1`

## PR Creation Attempts

| Method | Result |
|--------|--------|
| GitHub connector `_create_pull_request` | Failed with GitHub API 404 `Not Found`. |
| `gh pr create --draft` | Failed with GraphQL `Resource not accessible by personal access token (repository.defaultBranchRef)`. |
| `gh api repos/Kirrito-k423/AutoModelMigrate/pulls` | Failed with HTTP 422 `not all refs are readable`. |
| `gh api repos/Kirrito-k423/AutoModelMigrate/branches/...` | Failed with HTTP 403 `Resource not accessible by personal access token`. |

## Validation Before Ship

- Phase verification: passed.
- UAT: 5 passed, 0 issues.
- Security: 6 threats closed, 0 open.
- Working tree: clean before push.
- Branch push: succeeded over SSH.

## Manual PR Body

Title:

`Phase 5: Reference Case Packaging`

Body:

```markdown
## Summary

Packages Phase 5 of the AI Infra Migration Framework: reusable migration templates, next-case operating guidance, and the VeOmni + MiniMax M3 reference-case index.

## Changes

- Added the reusable framework template pack and lifecycle templates under `docs/framework/`.
- Added `docs/framework/next-case-guide.md` for future migration operators.
- Added `docs/cases/veomni-minimax-m3/reference-case.md` to separate case evidence from generic contracts.
- Updated reusable deltas and framework README navigation.
- Recorded Phase 5 verification, UAT, security, and closeout artifacts.

## Verification

- Phase completeness: passed for 2 plans / 2 summaries.
- YAML parsing: manifest, validation recipe, accuracy signoff, and performance profile templates parse successfully.
- Link and boundary checks: passed for template pack, next-case guide, reference-case index, reusable deltas, and README.
- UAT: 5 passed, 0 issues.
- Security: 6 threats closed, 0 open.

## Support Boundary

This is documentation and evidence packaging. It does not claim MiniMax M3 runs in VeOmni, that accuracy has been measured, or that Ascend NPU execution is ready. Runtime and support claims remain gated on backend, validation, accuracy, and performance evidence.

gate_status: skill=0, fallback=0, exempt=0, missing=0
```

## Next Action

Refresh the GitHub token or create the draft PR manually from the compare URL.
The intended target is a stacked PR from
`codex/phase-5-reference-case-packaging` into
`codex/phase-3-correctness-accuracy-harness`.
