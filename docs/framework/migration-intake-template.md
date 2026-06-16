# Migration Intake Template

**Migration name:** `<source> to <target> for <model-or-feature>`  
**Status:** Intake  
**Owner:** `<team/person>`  
**Date:** `<YYYY-MM-DD>`  
**Source references:** `<links, commits, artifact revisions>`

Use this template before implementation begins. The goal is to separate known facts, assumptions, backend constraints, validation gates, and signoff evidence so migration work does not become a one-off script.

## Scope

| Field | Value |
|-------|-------|
| Source Framework | `<framework, runtime, serving stack, or checkpoint format>` |
| Target Framework | `<framework or runtime receiving support>` |
| Model / Algorithm / Feature | `<model, algorithm, dataset, training feature, or inference feature>` |
| Training Scope | `<not in scope / smoke / fine-tune / pre-train / post-train / distributed>` |
| Inference Scope | `<not in scope / construction / forward / generation / serving>` |
| Accelerator Scope | `<CPU / GPU / NPU / mixed>` |
| First Slice | `<smallest useful vertical slice>` |
| Non-goals | `<what is explicitly out of scope>` |

## Source/Target

| Dimension | Source | Target | Notes |
|-----------|--------|--------|-------|
| Framework/runtime | `<source>` | `<target>` | `<version, commit, docs>` |
| Model implementation | `<source implementation>` | `<target integration point>` | `<reuse, wrap, or native adapter>` |
| Checkpoint format | `<format>` | `<loader/converter>` | `<hashes, shards, dtype>` |
| Dataset/data format | `<format>` | `<target loader>` | `<schema, transforms>` |
| Execution recipe | `<source command>` | `<target command>` | `<single device, distributed, serving>` |

## Model Or Feature Dimensions

| Area | Known Fact | Source | Assumption | Risk | Validation Needed |
|------|------------|--------|------------|------|-------------------|
| Architecture / algorithm | `<fact>` | `<URL/commit>` | `<assumption>` | `<risk>` | `<check>` |
| Tokenizer / preprocessing | `<fact>` | `<URL/commit>` | `<assumption>` | `<risk>` | `<check>` |
| Checkpoint / artifact | `<fact>` | `<URL/commit>` | `<assumption>` | `<risk>` | `<check>` |
| Precision / dtype | `<fact>` | `<URL/commit>` | `<assumption>` | `<risk>` | `<check>` |
| Context / sequence / shape limits | `<fact>` | `<URL/commit>` | `<assumption>` | `<risk>` | `<check>` |
| Modality / input contract | `<fact>` | `<URL/commit>` | `<assumption>` | `<risk>` | `<check>` |
| Custom operators / kernels | `<fact>` | `<URL/commit>` | `<assumption>` | `<risk>` | `<check>` |

## Target Framework Extension Points

| Area | Target Hook | Evidence | Required Adapter Work |
|------|-------------|----------|-----------------------|
| Model registration / construction | `<hook>` | `<file/doc>` | `<work>` |
| Config parsing | `<hook>` | `<file/doc>` | `<work>` |
| Tokenizer / processor | `<hook>` | `<file/doc>` | `<work>` |
| Data loading / transforms | `<hook>` | `<file/doc>` | `<work>` |
| Checkpoint load/save | `<hook>` | `<file/doc>` | `<work>` |
| Training recipe | `<hook>` | `<file/doc>` | `<work>` |
| Inference recipe | `<hook>` | `<file/doc>` | `<work>` |
| Distributed execution | `<hook>` | `<file/doc>` | `<work>` |

## Backend Scope

Model GPU, NPU, and other accelerator differences through backend capabilities. Avoid scattering backend-specific branches through model code.

| Capability | CPU | GPU | NPU | Evidence | Status |
|------------|-----|-----|-----|----------|--------|
| Device visibility | `<state>` | `<state>` | `<state>` | `<command/output>` | `<unsupported / blocked / emulated / native / optimized>` |
| Precision modes | `<state>` | `<state>` | `<state>` | `<docs/output>` | `<status>` |
| Custom operators / kernels | `<state>` | `<state>` | `<state>` | `<docs/output>` | `<status>` |
| Memory constraints | `<state>` | `<state>` | `<state>` | `<docs/output>` | `<status>` |
| Communication primitives | `<state>` | `<state>` | `<state>` | `<docs/output>` | `<status>` |
| Compile / graph constraints | `<state>` | `<state>` | `<state>` | `<docs/output>` | `<status>` |

## Dataset And Checkpoint Scope

| Artifact | Required Evidence | First Action |
|----------|-------------------|--------------|
| Source revision | `<commit/tag/revision>` | `<pin source>` |
| Config | `<hash/schema/classes>` | `<inspect>` |
| Tokenizer/processor | `<class, special tokens, transforms>` | `<fixture>` |
| Checkpoint | `<file list, shard index, dtype, naming>` | `<inspect>` |
| Dataset fixture | `<schema and sample>` | `<create tiny fixture>` |
| Reference output | `<logits/output/shape/dtype>` | `<run baseline or define invariant>` |

## Validation Gates

| Gate | Required Evidence | Blocking? |
|------|-------------------|-----------|
| Manifest gate | `<source, target, revision, scope, assumptions>` | Yes |
| Config gate | `<parse and schema mapping>` | Yes |
| Data/tokenizer gate | `<fixture parity>` | Yes |
| Checkpoint gate | `<load or mapped inspection>` | `<yes/no>` |
| Construction gate | `<target framework construction>` | Yes |
| Forward / inference gate | `<tiny output or invariant>` | `<yes/no>` |
| Accuracy gate | `<task-level metric and threshold>` | `<yes/no>` |
| Backend runtime gate | `<GPU/NPU readiness>` | `<yes/no>` |

## Performance Gates

Performance starts after correctness and accuracy gates are explicit.

| Stage | Evidence |
|-------|----------|
| Baseline | `<hardware, backend, dtype, batch, sequence, command>` |
| Profiling | `<latency, throughput, memory, utilization, bottleneck>` |
| Optimization hypothesis | `<expected improvement and risk>` |
| Before/after report | `<metrics and config diff>` |
| Regression guard | `<threshold and rerun command>` |

## Signoff

| Signoff Area | Required Evidence | Owner | Status |
|--------------|-------------------|-------|--------|
| Correctness | `<smoke/parity/logs>` | `<owner>` | `<status>` |
| Accuracy | `<metrics/drift>` | `<owner>` | `<status>` |
| Performance | `<profiling/report>` | `<owner>` | `<status>` |
| Backend support | `<capability matrix>` | `<owner>` | `<status>` |
| Documentation | `<runbook/templates>` | `<owner>` | `<status>` |

## Open Questions

- `<question>`

## First Vertical Slice Candidate

**Candidate:** `<smallest useful slice>`

**Why this slice:** `<rationale>`

**Blocking gaps:** `<gaps>`

**Non-goals:** `<non-goals>`
