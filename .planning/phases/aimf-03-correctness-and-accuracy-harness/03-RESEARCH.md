# Phase 3: Correctness And Accuracy Harness - Research

## RESEARCH COMPLETE

**Question answered:** What does Phase 3 need to design so migrations prove correctness and accuracy before optimization?

Phase 3 should turn the Phase 2 `ValidationSuite` and `Recipe` contracts into reusable validation artifacts. It remains a framework/documentation phase, not a full MiniMax M3 implementation. The useful output is a versioned validation recipe model, a correctness harness spec, an accuracy/drift signoff contract, and MiniMax M3 example artifacts that show how gates block lifecycle transitions.

## Inputs Read

- `.planning/ROADMAP.md`
- `.planning/REQUIREMENTS.md`
- `.planning/phases/aimf-03-correctness-and-accuracy-harness/03-CONTEXT.md`
- `docs/framework/migration-manifest-spec.md`
- `docs/framework/adapter-contracts.md`
- `docs/framework/backend-capability-matrix.md`
- `docs/framework/migration-lifecycle.md`
- `docs/framework/backlog-taxonomy.md`
- `docs/framework/examples/veomni-minimax-m3.manifest.yaml`
- `docs/cases/veomni-minimax-m3/gap-analysis.md`
- `docs/cases/veomni-minimax-m3/npu-runtime-evidence.md`

## Planning Implications

### 1. Correctness Harness

Correctness should be expressed as ordered gates, not a single smoke result:

1. Manifest/schema gate.
2. Model config and artifact inventory gate.
3. Tokenizer/processor fixture gate.
4. Checkpoint/index metadata gate.
5. Construction or tiny smoke gate.
6. Forward/logit parity gate.
7. Loss parity gate where training behavior is in scope.
8. Deterministic repeatability gate.
9. Backend runtime gate when a backend support claim is in scope.

For MiniMax M3, the first slice can be tiny and text-only while still recording deferred MSA, image/video, 1M context, and NPU gates.

### 2. Validation Recipe

A validation recipe should be manifest-addressable and reproducible. It needs:

- Migration id, model revision, target framework, backend, dtype, hardware, environment.
- Gate list with owner layer, command, fixture, expected evidence, blocking behavior, and lifecycle transition.
- Baseline and candidate references.
- Seed/determinism policy.
- Explicit unsupported or blocked runtime states.

### 3. Accuracy And Drift Signoff

Accuracy signoff should not be conflated with smoke or parity. It needs:

- Versioned baseline id and artifact hashes.
- Dataset/eval set version.
- Metric definitions and accepted thresholds.
- Numerical drift policy for logits/loss/metrics, including dtype/backend-specific tolerances.
- Evidence links and reviewer signoff.
- Rules for when a result blocks `Correctness`, `Scale`, `Optimize`, `Accuracy Signoff`, or `Production Ready`.

## Recommended Plan Shape

1. **Correctness validation harness:** Create a harness spec, validation recipe schema, and MiniMax M3 first-slice validation recipe example.
2. **Accuracy and drift signoff:** Create accuracy/drift signoff spec, schema/example artifact, and validation result lifecycle integration docs.

## Risks For Planning

- **False confidence from tiny smoke:** Tiny construction only proves wiring; the plan must keep accuracy signoff separate.
- **Backend blocker ambiguity:** NPU blockers must be recorded as blocked runtime gates, not failed correctness.
- **Thresholds without provenance:** Any tolerance or metric threshold must name baseline, dtype/backend, fixture, revision, and reviewer.
- **Overfitting to MiniMax M3:** Examples should use MiniMax M3, but schema/spec fields must remain reusable for other model/framework migrations.
