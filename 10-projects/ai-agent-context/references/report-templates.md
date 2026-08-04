# Report Templates

## Brief Health Report

```markdown
# Health Check: <application>

- **Environment:** <environment>
- **Namespace:** <namespace>
- **Status:** Healthy | Degraded | Failing | Stale | Unknown
- **Declared version:** <version or unavailable>
- **Live version:** <version or unavailable>
- **Checked at:** <timestamp>

## Findings

| Check | Result | Evidence |
|---|---|---|
| Workload readiness | | |
| Version alignment | | |
| Restart/failure signals | | |
| Recent warnings | | |

## Next action

<One practical next step.>
```

## Full Incident Report

```markdown
# Incident Diagnosis: <application or symptom>

## Scope

- Environment:
- Namespace:
- Time window:
- Impact:
- Current state:

## Timeline

| Time | Event | Evidence |
|---|---|---|

## Desired and live state

| Item | Desired | Live | Result |
|---|---|---|---|

## Observed facts

1.
2.

## Likely root cause

<Diagnosis>

- **Confidence:** High | Medium | Low
- **Supporting evidence:**
- **Contradicting evidence:**

## Unknowns

- 

## Immediate next steps

1.
2.

## Proposed remediation

<Not executed in production.>

## Prevention

- Monitoring:
- Testing:
- Platform or code improvement:
```

## Deployment Verification Report

```markdown
# Deployment Verification: <application/version>

| Stage | Expected | Observed | Status |
|---|---|---|---|
| Source commit | | | |
| CI build | | | |
| Artifact | | | |
| Deployment declaration | | | |
| CD execution | | | |
| Live workload | | | |

## Result

Fully deployed and healthy | Deployed but unhealthy | Declared but not applied | Built but not promoted | Failed | Unable to verify
```

## Code Review Finding

```markdown
### <Severity>: <finding title>

- **File:** `<path>:<line>`
- **Problem:**
- **Impact:**
- **Suggested correction:**
```

## IaC Review Summary

```markdown
# IaC Review

## Findings

<Prioritized findings>

## Blast radius

- Environments:
- Accounts/clusters:
- Shared resources:

## Validation

- Checks run:
- Checks not run:

## Deployment risk

Low | Medium | High | Critical

## Rollback assessment

<How reversible the change is and any data/state constraints.>
```

## Spark Diagnosis Addendum

```markdown
## Spark-specific evidence

| Area | Evidence | Interpretation |
|---|---|---|
| Custom resource | | |
| Driver | | |
| Executors | | |
| Scheduling/resources | | |
| Kafka/checkpoint | | |
| Iceberg/catalog | | |
| Operator/RBAC/ownership | | |
```
