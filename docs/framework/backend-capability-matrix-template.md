# Backend Capability Matrix Template

## Purpose

Use this template to record backend capability status before claiming support.
The canonical guidance is `docs/framework/backend-capability-matrix.md`.

Capability maturity values are `unsupported`, `emulated`, `native`, and
`optimized`. Use `blocked` only for runtime or evidence blockers that prevent a
capability from being tested.

## Status Guidance

| Status | Meaning |
|--------|---------|
| `unsupported` | No working path or accepted fallback exists for the capability. |
| `blocked` | A prerequisite such as runtime access, permission, package install, artifact access, or evidence capture is missing. |
| `emulated` | Capability works through a fallback or compatibility path with explicit caveats. |
| `native` | Capability works through the backend's intended implementation with evidence. |
| `optimized` | Capability has native support plus profiler-backed tuning and regression guards. |

## Capability Rows

| Area | Backend | Capability | Status | Evidence | Blockers | Validation target | Owner |
|------|---------|------------|--------|----------|----------|-------------------|-------|
| Framework feature | `<backend>` | `<model construction, execution recipe, distributed mode, serving hook>` | `<unsupported|blocked|emulated|native|optimized>` | `<doc, source inspection, command output>` | `<blocker id or none>` | `<gate name and required evidence>` | BackendAdapter |
| Backend feature | `<backend>` | `<device visibility, runtime init, tensor smoke, stream/event behavior>` | `<status>` | `<command output or runtime log>` | `<blocker id or none>` | `<runtime validation gate>` | BackendAdapter |
| Custom operator or kernel | `<backend>` | `<operator, attention mode, fused path, fallback>` | `<status>` | `<source, profiler, or correctness evidence>` | `<blocker id or none>` | `<correctness and performance gate>` | BackendAdapter |
| precision | `<backend>` | `<dtype or mixed precision mode>` | `<status>` | `<docs, command output, or validation report>` | `<blocker id or none>` | `<dtype parity or drift gate>` | BackendAdapter |
| memory | `<backend>` | `<capacity, allocation, cache, activation, checkpointing, long-shape budget>` | `<status>` | `<profile or runtime log>` | `<blocker id or none>` | `<memory stability gate>` | BackendAdapter |
| communication | `<backend>` | `<collectives, process group, host-device transfer, interconnect>` | `<status>` | `<distributed smoke or docs>` | `<blocker id or none>` | `<distributed validation gate>` | BackendAdapter |
| compile or graph behavior | `<backend>` | `<compiler, graph capture, shape policy, cache behavior>` | `<status>` | `<compile log or profiler output>` | `<blocker id or none>` | `<compile overhead gate>` | BackendAdapter |
| profiler hooks | `<backend>` | `<profiler tool, trace capture, counters, export path>` | `<status>` | `<trace, profile report, or command>` | `<blocker id or none>` | `<profile reproducibility gate>` | BackendAdapter |

## Runtime Blockers

| Blocker ID | Backend | Reason | Evidence | Next action | Blocks support claim? |
|------------|---------|--------|----------|-------------|-----------------------|
| `<blocker-id>` | `<backend>` | `<why the capability cannot be tested>` | `<doc, log, issue, command output>` | `<next evidence-producing action>` | `<yes/no>` |

## Review Checklist

- [ ] Every status has evidence or an explicit blocker.
- [ ] `blocked` rows explain the prerequisite and next action.
- [ ] Precision, memory, communication, compile, and profiler coverage are present.
- [ ] Backend claims are scoped to the exact hardware, runtime, dtype, workload,
  and validation target that produced evidence.
- [ ] Optimization claims link to a performance profile and regression guard.
