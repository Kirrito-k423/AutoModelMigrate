# Gap Analysis Template

**Migration name:** `<source> to <target> for <model-or-feature>`  
**Inputs:** `<intake file, assumption log, runtime evidence>`  
**Status:** Gap Analysis  
**Date:** `<YYYY-MM-DD>`

Use this template after the migration intake exists and before implementation planning. Every gap should have an owner layer, evidence, severity, first action, and blocking status.

## Severity Scale

| Severity | Meaning |
|----------|---------|
| Critical | Blocks the selected slice or can create false success. |
| High | Required before correctness or accuracy signoff. |
| Medium | Required before scale, backend support, or performance work. |
| Low | Hardening, documentation, or later automation. |

## Owner Layers

| Owner Layer | Responsibility |
|-------------|----------------|
| ModelSpec | Model/algorithm semantics independent of framework and backend. |
| FrameworkAdapter | Target framework registration, builders, recipes, and execution hooks. |
| BackendAdapter | CPU/GPU/NPU capabilities, runtime gates, precision, kernels, memory, and communication. |
| DataAdapter | Tokenizer, processor, dataset, checkpoint, artifact, and fixture handling. |
| ValidationSuite | Smoke, parity, correctness, accuracy, drift, and status gates. |
| OptimizationLoop | Profiling, bottleneck analysis, before/after reports, and performance regression guards. |

## ModelSpec Gaps

| Gap | Evidence | Severity | Owner Layer | Proposed First Action | Blocks Slice |
|-----|----------|----------|-------------|-----------------------|--------------|
| `<gap>` | `<source link, intake row, command output>` | `<Critical/High/Medium/Low>` | ModelSpec | `<first action>` | `<Yes/No>` |

## FrameworkAdapter Gaps

| Gap | Evidence | Severity | Owner Layer | Proposed First Action | Blocks Slice |
|-----|----------|----------|-------------|-----------------------|--------------|
| `<gap>` | `<source link, intake row, command output>` | `<Critical/High/Medium/Low>` | FrameworkAdapter | `<first action>` | `<Yes/No>` |

## BackendAdapter Gaps

| Gap | Evidence | Severity | Owner Layer | Proposed First Action | Blocks Slice |
|-----|----------|----------|-------------|-----------------------|--------------|
| `<gap>` | `<source link, runtime evidence, capability matrix>` | `<Critical/High/Medium/Low>` | BackendAdapter | `<first action>` | `<Yes/No>` |

## DataAdapter Gaps

| Gap | Evidence | Severity | Owner Layer | Proposed First Action | Blocks Slice |
|-----|----------|----------|-------------|-----------------------|--------------|
| `<gap>` | `<source link, artifact inspection, fixture output>` | `<Critical/High/Medium/Low>` | DataAdapter | `<first action>` | `<Yes/No>` |

## ValidationSuite Gaps

| Gap | Evidence | Severity | Owner Layer | Proposed First Action | Blocks Slice |
|-----|----------|----------|-------------|-----------------------|--------------|
| `<gap>` | `<intake gate, missing fixture, missing threshold>` | `<Critical/High/Medium/Low>` | ValidationSuite | `<first action>` | `<Yes/No>` |

## OptimizationLoop Gaps

| Gap | Evidence | Severity | Owner Layer | Proposed First Action | Blocks Slice |
|-----|----------|----------|-------------|-----------------------|--------------|
| `<gap>` | `<performance gate, profiler requirement, baseline gap>` | `<Critical/High/Medium/Low>` | OptimizationLoop | `<first action>` | `<Yes/No>` |

## Selected First Vertical Slice

**Slice name:** `<name>`

**Goal:** `<what this slice proves>`

### Slice Steps

1. `<step>`

### Rationale

- `<why this slice is small enough>`
- `<why this slice is meaningful>`
- `<what false-positive risk it prevents>`

### Blocking Gaps

| Blocking Gap | Owner Layer | Why It Blocks |
|--------------|-------------|---------------|
| `<gap>` | `<owner layer>` | `<reason>` |

### Non-goals

- `<explicit non-goal>`

## Output To Planning

| Output | Destination |
|--------|-------------|
| Blocking gaps | `<next implementation plan or backlog>` |
| Architecture inputs | `<ModelSpec / adapter / capability matrix design>` |
| Validation inputs | `<validation plan>` |
| Optimization inputs | `<performance plan>` |
| Runtime/setup inputs | `<ops runbook or BackendAdapter gate>` |
