---
name: veomni-minimax-m3-migration
description: Use when migrating MiniMax M3 into VeOmni, auditing a VeOmni MiniMax M3 PR, or preparing validation evidence for the MiniMax M3 VeOmni onboarding path. Covers source verification, VeOmni registry/config slices, transformers version gates, smoke reporting, and PR evidence.
---

# VeOmni MiniMax M3 Migration

Use this skill when the task is to move MiniMax M3 support forward in
`ByteDance-Seed/VeOmni` or to review whether a claimed MiniMax M3 migration is
real, reproducible, and PR-ready.

## Required Sources

Before editing code, verify current upstream state with authoritative sources:

```bash
curl -fsSL https://huggingface.co/api/models/MiniMaxAI/MiniMax-M3
curl -fsSL https://huggingface.co/MiniMaxAI/MiniMax-M3/raw/main/config.json
curl -fsSL https://huggingface.co/MiniMaxAI/MiniMax-M3/raw/main/preprocessor_config.json
curl -sS -o /tmp/minimax_m3_vl_v590.py -w '%{http_code}' \
  https://raw.githubusercontent.com/huggingface/transformers/v5.9.0/src/transformers/models/minimax_m3_vl/modeling_minimax_m3_vl.py
curl -sS -o /tmp/minimax_m3_vl_v512.py -w '%{http_code}' \
  https://raw.githubusercontent.com/huggingface/transformers/v5.12.0/src/transformers/models/minimax_m3_vl/modeling_minimax_m3_vl.py
```

Record the Hugging Face `sha`, `lastModified`, `model_type`, architecture,
processor class, and the HTTP status codes for the transformers source files.
As of the first migration slice, MiniMax M3 is `model_type=minimax_m3_vl`,
`architectures=["MiniMaxM3SparseForConditionalGeneration"]`, and the model
requires transformers MiniMax M3 VL sources that are absent from VeOmni's
`transformers==5.9.0` pin.

## VeOmni Intake Slice

For a truthful first PR, land only artifacts that are useful under the current
VeOmni dependency pin:

- `veomni/models/transformers/minimax_m3_vl/configuration_minimax_m3_vl.py`
  with local config classes that can parse MiniMax M3's top-level config,
  legacy `text_config.model_type=minimax_m2`, `sparse_attention_config`, and
  `moe_layer_freq`.
- `veomni/models/transformers/minimax_m3_vl/__init__.py` registering
  `MODEL_CONFIG_REGISTRY` entries and a fail-fast `MODELING_REGISTRY` entry
  explaining the transformers pin blocker.
- `veomni/models/transformers/minimax_m3_vl/parallel_plan.py` documenting the
  expected MoE expert parameter paths for top-level VLM and text-only layouts.
- `tests/toy_config/minimax_m3_vl_toy/config.json` with tiny dimensions and
  explicit sparse/dense layer dispatch coverage.
- A focused registry test that proves config parsing and proves the modeling
  blocker is explicit.
- A validation report in `docs/` with every command attempted and every
  failed or skipped gate.

Do not hand-write files under `generated/`. VeOmni's generated modeling files
must come from patchgen once the transformers source pin is moved to a release
that contains MiniMax M3 VL.

## Verification Commands

Always run the light checks first:

```bash
python3 -m py_compile \
  veomni/models/transformers/minimax_m3_vl/__init__.py \
  veomni/models/transformers/minimax_m3_vl/configuration_minimax_m3_vl.py \
  veomni/models/transformers/minimax_m3_vl/parallel_plan.py \
  tests/models/test_model_registry.py
python3 -m json.tool tests/toy_config/minimax_m3_vl_toy/config.json
  pytest tests/models/test_model_registry.py -k minimax_m3_vl -q
```

For the training-evidence gate, add and run a tiny SFT smoke that proves the
MiniMax M3 path can load:

- the MiniMax M3 toy config through VeOmni's config registry,
- a no-real-weights manifest that explicitly records random initialization,
- a local JSONL SFT dataset,
- a real backward/optimizer loop that emits a decreasing loss curve.

Use a command shaped like:

```bash
env PYTHONPATH="$PWD" uv run --no-project --python 3.11 \
  --with torch==2.7.1 \
  --with transformers==5.9.0 \
  --with matplotlib \
  --with numpy \
  --with psutil \
  --with packaging \
  --with safetensors \
  --with tqdm \
  python tests/train_scripts/train_minimax_m3_vl_sft_smoke.py \
  --config-path ./tests/toy_config/minimax_m3_vl_toy \
  --weights-manifest ./tests/fixtures/minimax_m3_vl_sft/random_init_weights_manifest.json \
  --dataset-path ./tests/fixtures/minimax_m3_vl_sft/tiny_sft.jsonl \
  --output-dir docs/usage/support_new_models/artifacts/minimax_m3_vl_sft_smoke \
  --steps 80 \
  --batch-size 4 \
  --lr 0.02 \
  --seed 20260617
```

The validation report should include the first and last loss, the JSON log,
and a rendered PNG loss curve. Treat this as a tiny random-init training proof,
not as full production MiniMax M3 VL patchgen modeling support.

If the host lacks VeOmni's required Python or dependencies, record that as
runtime evidence instead of claiming the gate passed. A useful fallback is a
minimal uv environment:

```bash
python3 -m pip install --user uv
uv python install 3.11
env PYTHONPATH="$PWD" uv run --no-project --python 3.11 \
  --with pytest --with transformers==5.9.0 --with torch==2.7.1 \
  --with numpy --with psutil --with rich --with packaging \
  pytest tests/models/test_model_registry.py -k minimax_m3_vl -q
```

## PR Evidence Checklist

The PR or handoff is incomplete until it has:

- VeOmni PR URL.
- Code diff summary with exact files touched.
- Smoke commands and logs.
- Official-source evidence for MiniMax M3 and transformers availability.
- Failure/blocker report that distinguishes local environment failures from
  upstream dependency blockers.
- Next patchgen step: move or relax the transformers pin, generate patched
  modeling from the MiniMax M3 VL source, then add forward/trainer smoke.
