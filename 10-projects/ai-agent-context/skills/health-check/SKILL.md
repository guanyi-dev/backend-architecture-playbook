---
name: health-check
description: Use for a fast, read-only assessment of service health, deployed version, workload readiness, and obvious warning signals.
---

# Platform Health Check

## Use when

- “Is this service healthy?”
- “What version is deployed?”
- “Are there failing Pods?”
- “Is the scheduled job running?”
- “Check dev/staging/prod status.”

## Do not use when

- The user reports a specific failure requiring root-cause analysis.
- The task is a code or IaC review.
- The user wants a remediation implementation.

Use `incident-diagnosis` for deeper investigation.

## Safety

Apply all global policies. Production is read-only.

Do not pull repositories or switch branches. Fetch remote refs only when desired-state comparison is required.

## Workflow

### 1. Resolve scope

Identify:

- Environment
- Cluster or platform
- Namespace
- Application or workload
- Expected version, if provided

### 2. Read desired state

Check the deployment repository or release metadata for:

- Declared image and version
- Replica count
- Workload type
- Important feature flags

Skip this step for a purely live-state request if repository context is unavailable.

### 3. Inspect live workload

Use the smallest relevant command set:

```bash
kubectl get deployment,statefulset,job,cronjob,pod -n <namespace>
kubectl get pod -n <namespace> -l app=<app> -o wide
```

Inspect:

- Ready versus desired replicas
- Pod phase
- Restart counts
- Age
- Job completion
- Recent scheduling

### 4. Check version

Inspect image tags or digests:

```bash
kubectl get pod <pod> -n <namespace> -o jsonpath='{.spec.containers[*].image}'
```

Compare declared and live versions when both are available.

### 5. Inspect warnings only when needed

If anything is unhealthy or stale:

```bash
kubectl get events -n <namespace> --sort-by=.lastTimestamp --field-selector type=Warning
```

Do not perform full log analysis unless the health result cannot be explained without it.

## Output

Use the brief health report in `references/report-templates.md`.

Include:

- Overall state: Healthy, Degraded, Failing, Stale, or Unknown
- Declared and live version
- Replica/job status
- Relevant warnings
- One recommended next action

## Stop condition

Stop when the health state and version are established, or when available access cannot resolve them.
