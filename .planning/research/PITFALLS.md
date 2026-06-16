# Pitfalls Research

**Domain:** AI infrastructure migration framework
**Researched:** 2026-06-16
**Confidence:** MEDIUM

## Critical Pitfalls

### Pitfall 1: Runnable But Semantically Wrong

**What goes wrong:**
The migrated model executes, but tokenizer behavior, checkpoint mapping, attention masks, precision, rotary/position logic, or data preprocessing differs from the source path.

**Why it happens:**
Teams treat successful execution as proof of migration.

**How to avoid:**
Require fixture-level parity and task-level accuracy gates before performance work.

**Warning signs:**
No golden fixtures; no explicit drift thresholds; performance tuning starts before forward/loss parity.

**Phase to address:**
Phase 3: Correctness and Accuracy Harness.

---

### Pitfall 2: Backend Forking

**What goes wrong:**
GPU and NPU paths diverge into separate model implementations.

**Why it happens:**
Backend constraints are patched directly into model code.

**How to avoid:**
Use BackendAdapter and capability matrix boundaries.

**Warning signs:**
Model files contain accelerator conditionals; unsupported features are undocumented.

**Phase to address:**
Phase 2 and Phase 4.

---

### Pitfall 3: Long-Context Memory Surprise

**What goes wrong:**
Small-context smoke tests pass, but MiniMax M3's 1M-context path fails due to memory, sparse attention backend gaps, cache layout, or compile overhead.

**Why it happens:**
Teams validate only small toy sequences.

**How to avoid:**
Stage long-context gates: tiny smoke -> medium context -> target context, each with memory and latency evidence.

**Warning signs:**
No separate long-context test tier; no KV/cache or sparse-attention profiling plan.

**Phase to address:**
Phase 1 identifies scope; Phase 4 handles optimization.

---

### Pitfall 4: Multimodal Data Drift

**What goes wrong:**
Text path works, but image/video/multimodal preprocessing diverges.

**Why it happens:**
Data adapters are treated as incidental helpers.

**How to avoid:**
Make DataAdapter a first-class layer with fixtures for every modality in scope.

**Warning signs:**
No multimodal fixture contract; validation only covers text.

**Phase to address:**
Phase 3.

## Technical Debt Patterns

| Shortcut | Immediate Benefit | Long-term Cost | When Acceptable |
|----------|-------------------|----------------|-----------------|
| One-off conversion scripts | Fast first demo | Cannot reuse or audit | Only exploratory spikes. |
| Backend conditionals in model files | Quick GPU/NPU patch | Forked behavior and fragile tests | Never in production path. |
| Performance reports in chat/logs only | Low ceremony | Evidence disappears | Never for signoff. |

## Performance Traps

| Trap | Symptoms | Prevention | When It Breaks |
|------|----------|------------|----------------|
| Sparse attention fallback to dense path | Huge memory and prefill latency | Capability check for MSA support | Medium/long context. |
| Compile overhead ignored | Good steady-state but bad startup | Track compile and warmup separately | Inference rollout. |
| Communication bottleneck hidden | Low accelerator utilization | Profile collectives and parallelism | Distributed training. |

## Sources

- MiniMax Sparse Attention paper: https://arxiv.org/html/2606.13392v1
- Together AI MiniMax M3 serving analysis: https://www.together.ai/blog/serving-minimax-m3-for-efficient-inference-unlocking-1m-token-context-and-multimodality-without-regrets
- VeOmni paper: https://arxiv.org/html/2508.02317v3

---
*Pitfalls research for: AI Infra Migration Framework*
*Researched: 2026-06-16*
