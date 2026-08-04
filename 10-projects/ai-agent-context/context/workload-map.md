# Workload Map

## Purpose

Describe common workload types, their health signals, and the resources that normally own them.

| Workload type | Primary resource | Child resources | Key health signals |
|---|---|---|---|
| Stateless service | Deployment | ReplicaSet, Pods, Service, Ingress | Available replicas, readiness, error rate, latency |
| Stateful service | StatefulSet | Pods, PVCs, Services | Pod ordering, storage health, leader/quorum state |
| Scheduled job | CronJob | Jobs, Pods | Last schedule, completion, duration, missed runs |
| One-time batch job | Job | Pods | Completion, retries, failure reason |
| Argo Rollout | Rollout CR | ReplicaSets, Pods, Services | Step status, analysis result, stable/canary versions |
| Spark job | SparkApplication or ScheduledSparkApplication | Driver Pod, executor Pods, Services, ConfigMaps | Driver state, executor loss, application state, duration |
| Operator/controller | Deployment | Managed custom resources | Reconciliation errors, work queue depth, API permissions |
| Event consumer | Deployment or StatefulSet | Pods | Consumer lag, partition assignment, rebalance frequency |
| Data catalog | Deployment/StatefulSet | DB connections, Services | API availability, DB connectivity, commit conflicts |

## Version signals

Use the most authoritative available signal:

1. Immutable image digest
2. Container image tag
3. Standard version labels
4. Deployment annotation
5. Application startup log

Do not rely only on Pod names.

## Ownership signals

Inspect `metadata.ownerReferences` when child resources remain after their parent is removed.

Typical chains:

```text
Deployment → ReplicaSet → Pod
CronJob → Job → Pod
SparkApplication → Driver Pod / Service / ConfigMap
StatefulSet → Pod / PVC
```

Missing or incorrect owner references can create orphaned resources.

## Health categories

Classify findings as:

- Healthy
- Degraded
- Failing
- Stale
- Unknown due to access or missing telemetry
