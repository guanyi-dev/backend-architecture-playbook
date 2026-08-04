---
name: incident-diagnosis
description: Diagnose failing or degraded engineering workloads by correlating desired state, deployment history, live state, events, logs, and dependency signals. Read-only in production.
---

# Incident Diagnosis

## Use when

- A workload is crashing, restarting, unavailable, slow, or producing incorrect results.
- A deployment appears unhealthy.
- A scheduled or streaming job is failing.
- A user asks for root cause, not just status.

## Inputs

Required:

- Application, component, or symptom

Resolve when possible:

- Environment
- Namespace
- Time window
- Expected behavior
- Recent release or ticket
- Customer or downstream impact

## Safety

Apply all policies. Production is strictly read-only.

Read only the minimum logs and configuration necessary. Redact sensitive values.

## Investigation model

Correlate these layers:

1. Business or ticket intent
2. Application and infrastructure code intent
3. Build and deployment history
4. Live workload state
5. Events and logs
6. Dependency and telemetry signals

## Workflow

### 1. Define scope and impact

Record:

- Environment and namespace
- Affected workload
- Start time and duration
- User-visible impact
- Whether the issue is ongoing

### 2. Establish a timeline

Collect recent:

- Releases
- Deployment runs
- Configuration changes
- Infrastructure changes
- Incident or ticket updates

Do not assume the most recent change caused the issue.

### 3. Compare desired and applied state

Inspect:

- Declared version
- Live image
- Replica settings
- Environment values
- Relevant dependency configuration

Flag drift explicitly.

### 4. Inspect workload state

Use workload-appropriate resources from `context/workload-map.md`.

Look for:

- Unready replicas
- Restart loops
- Failed jobs
- Scheduling failures
- Resource termination reasons
- Stale or orphaned resources

### 5. Inspect Events

Prioritize warnings near the failure time.

Common signals:

- OOMKilled
- FailedScheduling
- FailedMount
- ImagePullBackOff
- BackOff
- Evicted
- Probe failures
- Permission denied

### 6. Inspect minimal relevant logs

Use current and previous container logs when a restart occurred:

```bash
kubectl logs <pod> -n <namespace> --tail=100
kubectl logs <pod> -n <namespace> --previous --tail=100
```

Extract the smallest root-cause signal that supports the diagnosis.

### 7. Check dependencies

Only inspect dependencies supported by evidence, such as:

- Database connectivity
- Kafka or queue availability and lag
- Object storage access
- Catalog service availability
- DNS and ingress
- External APIs
- Certificate or secret delivery

### 8. Build and test hypotheses

For each hypothesis, state:

- Supporting evidence
- Contradicting evidence
- Missing evidence
- Confidence

Prefer the hypothesis that explains the largest number of observed facts with the fewest unsupported assumptions.

### 9. Produce next steps

Separate:

- Safe read-only checks
- Proposed remediation
- Human-run production actions
- Long-term prevention

Do not execute remediation in production.

## Output

Use the full incident report in `references/report-templates.md`.

Every diagnosis must include:

- Observed facts
- Inferences
- Unknowns
- Likely root cause
- Confidence level
- Immediate next step
- Prevention recommendation

## Stop condition

Stop when:

- The likely cause is supported by at least two independent signals, or
- Additional checks are blocked by access, or
- Continuing would require a mutation or sensitive data access.
