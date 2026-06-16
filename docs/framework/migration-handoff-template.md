# Migration Handoff Template

## Metadata

| Field | Value |
|-------|-------|
| Migration | `<migration-id>` |
| Date | `<YYYY-MM-DD>` |
| Owner | `<team/person>` |
| Target audience | `<infra team, model team, runtime team, serving team>` |
| Linked manifest | `<manifest path or URI>` |
| Linked capability matrix | `<capability matrix path or URI>` |

## Scope

| Area | Status | Notes |
|------|--------|-------|
| Source | `<pinned|pending|blocked>` | `<source revision and evidence>` |
| Target | `<pinned|pending|blocked>` | `<target revision and evidence>` |
| First slice | `<declared|complete|blocked>` | `<slice summary>` |
| Non-goals | `<recorded|missing>` | `<what is excluded>` |

## evidence inventory

| Evidence | Path or URI | Required for | Status |
|----------|-------------|--------------|--------|
| Intake | `<path>` | Intake | `<present|missing>` |
| Gap analysis | `<path>` | Gap Analysis | `<present|missing>` |
| Manifest | `<path>` | Adapter Build | `<present|missing>` |
| Backend capability matrix | `<path>` | Backend support claims | `<present|missing>` |
| Validation recipe and results | `<path>` | Correctness | `<present|missing>` |
| Accuracy signoff | `<path>` | Accuracy Signoff | `<present|missing>` |
| Performance profile | `<path>` | Scale or Optimize | `<present|missing>` |
| Optimization report | `<path>` | Optimize | `<present|missing|not in scope>` |
| Runtime runbook | `<path>` | Backend readiness | `<present|missing|not in scope>` |

## lifecycle state

| Current state | Latest passed transition | Blocked transition | Rationale |
|---------------|--------------------------|--------------------|-----------|
| `<Intake|Gap Analysis|Adapter Build|Correctness|Scale|Optimize|Accuracy Signoff|Production Ready>` | `<transition>` | `<transition or none>` | `<evidence-backed rationale>` |

## validation status

| Gate | Result | Blocks transition? | Evidence |
|------|--------|--------------------|----------|
| `<gate>` | `<pending|passed|failed|blocked|skipped|accepted_risk>` | `<yes/no>` | `<path or URI>` |

## accuracy status

| Metric | Threshold | Result | Drift | Decision |
|--------|-----------|--------|-------|----------|
| `<metric>` | `<threshold>` | `<value or pending>` | `<drift summary>` | `<pending|approved|rejected|accepted_risk>` |

## performance status

| Workload | Backend | Throughput | Latency | Memory | Runtime stability | Evidence |
|----------|---------|------------|---------|--------|-------------------|----------|
| `<slice>` | `<backend>` | `<value>` | `<value>` | `<value>` | `<pending|passed|failed|blocked>` | `<profile path or URI>` |

## backend capability

| Backend | Capability | Status | Support claim allowed? | Evidence |
|---------|------------|--------|------------------------|----------|
| `<backend>` | `<capability>` | `<unsupported|blocked|emulated|native|optimized>` | `<yes/no>` | `<capability matrix row or URI>` |

## unresolved blockers

| Blocker | Owner layer | Scope | Next action | Blocks release? |
|---------|-------------|-------|-------------|-----------------|
| `<blocker>` | `<owner layer>` | `<model|framework|backend|data|recipe|validation|optimization>` | `<next action>` | `<yes/no>` |

## reusable deltas

| Delta | Reusable contract affected | Destination | Status |
|-------|----------------------------|-------------|--------|
| `<lesson, template update, schema gap, or process improvement>` | `<contract or template>` | `<docs/framework or backlog path>` | `<open|captured|deferred>` |

## reviewer signoff

| Reviewer | Area | Decision | Date | Notes |
|----------|------|----------|------|-------|
| `<reviewer>` | `<correctness|accuracy|performance|backend|docs>` | `<approved|rejected|accepted_risk|blocked>` | `<YYYY-MM-DD>` | `<notes>` |

## Final Decision

`<Ready, blocked, accepted risk, or not ready. State the exact support scope and
which claims are intentionally not made.>`
