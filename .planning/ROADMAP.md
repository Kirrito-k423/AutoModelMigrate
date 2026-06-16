# Roadmap: AI Infra Migration Framework

## Overview

The roadmap starts with a real integration slice, then extracts reusable framework contracts from the work. Phase 1 grounds the project in VeOmni and MiniMax M3, records the GitHub publication target, and captures the current Ascend NPU runtime setup path as a reusable skill. Phase 2 turns the repeated migration concerns into framework architecture. Phase 3 defines correctness and accuracy gates. Phase 4 defines performance optimization and accelerator portability. Phase 5 packages the first MiniMax M3 vertical slice as the reference case and prepares the next migrations.

## Phases

- [x] **Phase 1: VeOmni + MiniMax M3 Intake** - Research the target model/framework pair, define the smallest useful first slice, and capture GitHub/NPU runtime readiness. (completed 2026-06-16)
- [x] **Phase 2: Core Migration Architecture** - Design manifests, adapters, capability matrices, and lifecycle states. (completed 2026-06-16)
- [ ] **Phase 3: Correctness and Accuracy Harness** - Define reproducible validation recipes and parity gates.
- [ ] **Phase 4: Backend Optimization Loop** - Define GPU/NPU capability modeling, profiling workflow, and performance signoff.
- [ ] **Phase 5: Reference Case Packaging** - Package VeOmni + MiniMax M3 outputs into reusable templates and next-case guidance.

## Phase Details

### Phase 1: VeOmni + MiniMax M3 Intake

**Goal**: Produce a source-backed intake dossier, gap map, first vertical-slice plan, GitHub publication path, and Ascend NPU runtime readiness plan for MiniMax M3 in VeOmni.
**Depends on**: Nothing (first phase)
**Requirements**: M3-01, M3-02, M3-03, M3-04, ACC-04, OPS-01
**Success Criteria** (what must be TRUE):

  1. The project has a documented MiniMax M3 assumption map covering model, data, checkpoint, attention, context, multimodal, training, and inference dimensions.
  2. The project has a VeOmni extension-point map covering where model, data, distributed recipe, checkpoint, and backend integration work would land.
  3. The first useful vertical slice is explicitly scoped with non-goals and validation gates.
  4. The GitHub publication target under `Kirrito-k423/AutoModelMigrate` is documented and the local remote is ready for an authenticated push.
  5. The Ascend NPU runtime path is captured as reusable skill-backed guidance with current-host evidence and explicit CANN/PTA gates.

**Plans**: 4 plans
Plans:
**Wave 1**

- [x] 01-01: Collect source-backed facts and open assumptions for VeOmni and MiniMax M3.
- [x] 01-04: Capture GitHub publication path and Ascend NPU runtime readiness skill/evidence.

**Wave 2** *(blocked on Wave 1 completion)*

- [x] 01-02: Map migration gaps and choose first vertical slice.

**Wave 3** *(blocked on Wave 2 completion)*

- [x] 01-03: Draft case-specific intake artifacts and reusable template deltas.

### Phase 2: Core Migration Architecture

**Goal**: Define the reusable framework architecture and contracts.
**Depends on**: Phase 1
**Requirements**: ARCH-01, ARCH-02, ARCH-03, ARCH-04, FLOW-01, FLOW-02, FLOW-04, ACC-01, ACC-02, ACC-03
**Success Criteria** (what must be TRUE):

  1. A migration manifest schema exists with clear ownership and example values from MiniMax M3.
  2. Adapter interfaces are defined without binding model logic to accelerator-specific code.
  3. Capability matrices can represent unsupported, emulated, native, and optimized states.

**Plans**: 3 plans
Plans:

- [x] 02-01: Define domain model and manifest schema.
- [x] 02-02: Define adapter boundaries and capability descriptors.
- [x] 02-03: Define migration lifecycle and backlog taxonomy.

### Phase 3: Correctness and Accuracy Harness

**Goal**: Define validation recipes that prove semantic migration correctness before optimization.
**Depends on**: Phase 2
**Requirements**: FLOW-03, VAL-01, VAL-02
**Success Criteria** (what must be TRUE):

  1. Smoke, unit, parity, and task-level evaluation recipes are specified.
  2. Accuracy and numerical drift thresholds are represented as versioned artifacts.
  3. Validation results can block migration status transitions.

**Plans**: 2 plans

Plans:

- [ ] 03-01: Design correctness validation harness.
- [ ] 03-02: Design accuracy and drift signoff artifacts.

### Phase 4: Backend Optimization Loop

**Goal**: Define GPU/NPU optimization workflow driven by profiler evidence and backend capability descriptors.
**Depends on**: Phase 3
**Requirements**: VAL-03, VAL-04, ACC-01, ACC-02, ACC-03
**Success Criteria** (what must be TRUE):

  1. Backend profiles capture memory, throughput, latency, utilization, compile overhead, and stability.
  2. Optimization plans record baseline, hypothesis, change, result, and rollback criteria.
  3. GPU and NPU differences are represented as backend capabilities rather than model forks.

**Plans**: 2 plans

Plans:

- [ ] 04-01: Define profiling and performance report schema.
- [ ] 04-02: Define backend-specific optimization workflow.

### Phase 5: Reference Case Packaging

**Goal**: Turn the VeOmni + MiniMax M3 case into reusable templates and operating guidance.
**Depends on**: Phase 4
**Requirements**: M3-04
**Success Criteria** (what must be TRUE):

  1. The project includes templates for future model/framework migrations.
  2. MiniMax M3-specific findings are separated from reusable framework contracts.
  3. The next migration can start from documented intake, gap, validation, and optimization templates.

**Plans**: 2 plans

Plans:

- [ ] 05-01: Package reusable migration templates.
- [ ] 05-02: Produce next-case handoff guide.

## Progress

**Execution Order:**
Phases execute in numeric order: 1 -> 2 -> 3 -> 4 -> 5

| Phase | Plans Complete | Status | Completed |
|-------|----------------|--------|-----------|
| 1. VeOmni + MiniMax M3 Intake | 4/4 | Complete   | 2026-06-16 |
| 2. Core Migration Architecture | 3/3 | Complete    | 2026-06-16 |
| 3. Correctness and Accuracy Harness | 0/2 | Not started | - |
| 4. Backend Optimization Loop | 0/2 | Not started | - |
| 5. Reference Case Packaging | 0/2 | Not started | - |
