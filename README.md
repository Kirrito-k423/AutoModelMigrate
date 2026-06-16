# AI Infra Migration Framework

This workspace is initialized as a GSD project for designing a reusable framework for AI infrastructure migration work: framework-to-framework model support, dataset support, training/inference feature migration, GPU/NPU portability, performance optimization, and accuracy validation.

The first case is VeOmni + MiniMax M3. Start with:

- `.planning/PROJECT.md`
- `.planning/REQUIREMENTS.md`
- `.planning/ROADMAP.md`
- `.planning/phases/01-veomni-minimax-m3-intake/01-CONTEXT.md`
- `.planning/phases/01-veomni-minimax-m3-intake/01-RESEARCH.md`
- `.planning/phases/01-veomni-minimax-m3-intake/01-01-PLAN.md`

GSD Core is installed through the official Codex installer at `$HOME/.codex` (`@opengsd/gsd-core` v1.4.5). Node.js is installed in the user environment at `$HOME/.local/node`, with `node`, `npm`, and `npx` linked from `$HOME/.local/bin`.

Repository publication target: `https://github.com/Kirrito-k423/AutoModelMigrate.git` (local `origin` is configured; `gh` 2.94.0 is installed under `$HOME/.local`, and authenticated push still depends on `gh auth login` or equivalent GitHub credentials).

Ascend NPU runtime setup is tracked through the global Codex skill at `$HOME/.codex/skills/ascend-npu-runtime`. Use it to inspect and accumulate CANN, PTA/torch_npu, root-only `npu-smi`/DCMI behavior, and verification gate knowledge before running VeOmni/MiniMax M3 on NPU. CANN should be downloaded from the official HiAscend community download page only after confirming the version matrix and download budget.
