# Optimization Report Template

## Metadata

| Field | Value |
|-------|-------|
| Migration | `<migration-id>` |
| Optimization ID | `<optimization-id>` |
| Date | `<YYYY-MM-DD>` |
| Owner | `<team/person>` |
| Backend / dtype / hardware | `<backend, dtype, hardware class>` |
| Workload slice | `<slice and shape>` |
| Linked manifest | `<manifest path or URI>` |
| Linked performance profile | `<performance profile path or URI>` |
| Linked validation recipe | `<validation recipe path or URI>` |

## Precondition

Correctness must already be green for the scoped workload before this report can
accept an optimization result.

| Gate | Evidence | Status |
|------|----------|--------|
| Correctness gate | `<validation report, command output, or artifact>` | `<pending|passed|failed|blocked>` |
| Accuracy or drift guard | `<signoff or guard evidence>` | `<pending|passed|failed|blocked>` |
| Backend capability row | `<capability matrix row>` | `<unsupported|blocked|emulated|native|optimized>` |

## baseline

| Metric | Value | Evidence |
|--------|-------|----------|
| Throughput | `<value and unit>` | `<profile/log>` |
| Latency | `<p50/p95/p99>` | `<profile/log>` |
| Memory | `<peak/reserved/activation/cache>` | `<profile/log>` |
| Compile overhead | `<value>` | `<profile/log>` |
| Runtime stability | `<duration/incidents>` | `<profile/log>` |

## hypothesis

`<State the bottleneck, expected improvement, affected backend, risk, and why the
change should help.>`

## Change

| Area | Before | After | Risk |
|------|--------|-------|------|
| Code or config | `<before>` | `<after>` | `<risk>` |
| Runtime or backend setting | `<before>` | `<after>` | `<risk>` |
| Recipe or workload | `<before>` | `<after>` | `<risk>` |

## config diff

```diff
<paste the minimal reproducible config or command diff>
```

## before/after metrics

| Metric | Before | After | Delta | Acceptance threshold | Decision |
|--------|--------|-------|-------|----------------------|----------|
| Throughput | `<value>` | `<value>` | `<delta>` | `<threshold>` | `<pass/fail>` |
| Latency | `<value>` | `<value>` | `<delta>` | `<threshold>` | `<pass/fail>` |
| Memory | `<value>` | `<value>` | `<delta>` | `<threshold>` | `<pass/fail>` |
| Compile overhead | `<value>` | `<value>` | `<delta>` | `<threshold>` | `<pass/fail>` |
| Runtime stability | `<value>` | `<value>` | `<delta>` | `<threshold>` | `<pass/fail>` |

## correctness regression

| Check | Command or artifact | Result | Blocks acceptance? |
|-------|---------------------|--------|--------------------|
| `<correctness check>` | `<command, log, or report>` | `<pending|passed|failed|blocked>` | `<yes/no>` |
| `<drift check>` | `<command, log, or report>` | `<pending|passed|failed|blocked>` | `<yes/no>` |

## acceptance

| Decision | Rationale | Reviewer |
|----------|-----------|----------|
| `<accepted|rejected|accepted-risk|blocked>` | `<why the result is safe or not safe>` | `<reviewer>` |

## rollback

| Rollback trigger | Rollback action | Evidence to attach |
|------------------|-----------------|--------------------|
| `<correctness regression, drift, instability, or performance miss>` | `<revert or config change>` | `<log, report, or issue>` |

## backlog handoff

| Item | Owner layer | Severity | First action | Evidence | Status |
|------|-------------|----------|--------------|----------|--------|
| `<unresolved bottleneck or blocker>` | `<OptimizationLoop|BackendAdapter|ValidationSuite>` | `<Critical|High|Medium|Low>` | `<next action>` | `<evidence URI>` | `<open|blocked|deferred|closed>` |

## Notes

- `<additional notes>`
