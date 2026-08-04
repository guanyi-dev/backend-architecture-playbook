# Common Kubernetes Failure Signals

Use this reference during health checks and incident diagnosis.

| Signal | Typical meaning | Confirm with | Common next checks |
|---|---|---|---|
| `CrashLoopBackOff` | Container repeatedly exits | Current and previous logs, termination code | Startup config, dependency failure, exception |
| `OOMKilled` | Container exceeded memory limit | `describe pod`, terminated state | Heap, off-heap, memory overhead, input growth |
| `ImagePullBackOff` | Image unavailable or unauthorized | Pod Events | Image name, tag, registry auth |
| `CreateContainerConfigError` | Missing ConfigMap, Secret, or invalid env reference | Pod Events | Referenced object names and namespace |
| `FailedScheduling` | No node satisfies constraints or resources | Pod Events | Requests, taints, affinity, quotas, PVCs |
| `Evicted` | Node pressure or policy removed Pod | Pod status and Events | Memory, disk, ephemeral storage, node pressure |
| `FailedMount` | Volume or secret mount failed | Pod Events | CSI driver, object existence, permissions |
| Readiness probe failed | App is running but not ready for traffic | Pod Events, app logs | Dependency readiness, probe path, startup duration |
| Liveness probe failed | App was restarted because health check failed | Events, previous logs | Deadlock, long GC, incorrect probe thresholds |
| Pending Pod | Scheduling or volume issue | Events | Capacity, selectors, quotas, PVC binding |
| Completed Pods accumulating | Cleanup, retention, or ownership problem | Parent resources, ownerReferences | CronJob history, TTL, operator GC, RBAC |
| High restart count | Repeated app or node-level instability | Previous logs, Events | OOM, probes, process exit, node disruption |
| Service has no endpoints | Selectors do not match ready Pods | Service, EndpointSlice, Pod labels | Label mismatch, readiness failure |
| Ingress 502/503 | Backend unavailable or misconfigured | Ingress events, service endpoints | Port mapping, readiness, target health |
| Permission denied | Service account or cloud identity lacks access | Logs, RBAC/IAM policy | Binding, role, identity annotation |

## Resource interpretation

### Memory

Distinguish:

```text
Application heap error
Container OOMKilled
Node memory pressure eviction
```

They require different fixes.

### CPU

CPU limits usually throttle rather than kill a container. Look for increased latency, missed heartbeats, slow GC, and probe failures.

### Ownership

Check child resource ownership:

```bash
kubectl get <resource> <name> -o jsonpath='{.metadata.ownerReferences}'
```

A missing owner reference can explain orphaned resources after parent deletion.

### Events

Events are useful but expire. Absence of an Event does not prove an issue did not occur.
