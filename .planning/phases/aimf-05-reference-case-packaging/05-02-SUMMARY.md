---
phase: aimf-05-reference-case-packaging
plan: "05-02"
subsystem: next-case-and-reference-case
tags: [runbook, reference-case, minimax-m3, template-packaging]
completed: 2026-06-16
status: completed
requirements-completed:
  - M3-04
---

# Phase 05 Plan 02: Next Case And Reference Case Summary

## Commits

| Task | Commit | Notes |
|------|--------|-------|
| Next-case guide | `f126e71` | Added operator runbook for template order, first-slice selection, runtime blocker routing, and ready-to-plan checks. |
| Reference case index | `4f7ccb6` | Packaged VeOmni + MiniMax M3 as case evidence and generic contract examples without support claims. |
| Deltas and README links | `2d38c56` | Refreshed reusable deltas and linked the next-case guide and reference case from the framework index. |

## Files Created Or Modified

- `docs/framework/next-case-guide.md` - Operator runbook for starting the next migration.
- `docs/cases/veomni-minimax-m3/reference-case.md` - Reference-case index separating case evidence from generic contracts.
- `docs/cases/veomni-minimax-m3/reusable-deltas.md` - Phase 5 packaging deltas and reference-case boundary notes.
- `docs/framework/README.md` - Links to the next-case guide and reference-case index.
- `.planning/phases/aimf-05-reference-case-packaging/05-02-SUMMARY.md` - This summary.

## Deviations

No scope deviations. Runtime and support-claim wording stayed conservative:
MiniMax M3, VeOmni, Ascend NPU, MSA, 1M context, multimodal fixtures, and host
runtime blockers are kept under case docs or worked examples.

## Command Evidence

- `rg "template-pack|intake|gap analysis|manifest|backend capability|validation|accuracy|performance|optimization|handoff|first slice|runtime blocker|correctness-before-optimization|ready to plan" docs/framework/next-case-guide.md` - passed.
- `rg "reference case|case evidence|generic contract|support status|not a support claim|MiniMax M3|VeOmni|Ascend NPU|blocked|reusable lesson" docs/cases/veomni-minimax-m3/reference-case.md` - passed.
- `rg "Phase 5|validation recipe|accuracy signoff|performance profile|optimization report|handoff|reference-case|generic defaults" docs/cases/veomni-minimax-m3/reusable-deltas.md` - passed.
- `rg "next-case-guide|reference-case|worked example|support claims|runtime evidence" docs/framework/README.md` - passed.
- `rg "ready to plan|first slice|runtime blocker|correctness-before-optimization" docs/framework/next-case-guide.md && rg "not a support claim|case evidence|generic contract|blocked" docs/cases/veomni-minimax-m3/reference-case.md && rg "Phase 5|validation recipe|accuracy signoff|performance profile|optimization report|handoff" docs/cases/veomni-minimax-m3/reusable-deltas.md && rg "next-case-guide|reference-case|worked example|runtime evidence" docs/framework/README.md` - passed.
- `git diff --check` - passed.

## Self-Check

- [x] Frontmatter present.
- [x] Commit table present.
- [x] Deviations documented.
- [x] Command evidence listed.
- [x] Next-case guide starts from the Phase 5 template pack.
- [x] Reference case states it is not a support claim.
- [x] README links the guide and reference case.
