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

Repository publication target: `https://github.com/Kirrito-k423/AutoModelMigrate` (published on `master`; local `origin` uses SSH-over-443 at `ssh://git@ssh.github.com:443/Kirrito-k423/AutoModelMigrate.git`).

Operational publishing details are tracked in `docs/ops/github-publish.md`, including the SSH-over-443 remote, write deploy key route, branch policy, and push verification.

Ascend NPU runtime setup is tracked through the global Codex skill at `$HOME/.codex/skills/ascend-npu-runtime`. Use it to inspect and accumulate CANN, PTA/torch_npu, root-only `npu-smi`/DCMI behavior, and verification gate knowledge before running VeOmni/MiniMax M3 on NPU. CANN should be downloaded from the official HiAscend community download page only after confirming the version matrix and download budget.

For Ascend A2/910B Docker setup, start from VeOmni's upstream guide: `docs/hardware_support/AscendDockerUsage/build_a2_docker.md` in `ByteDance-Seed/VeOmni`.
