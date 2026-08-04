# Spark Failure Signals

## Driver versus executor

### Driver failures

Common signals:

- SparkContext cannot initialize
- Streaming query terminates
- Catalog or object storage authentication fails
- Driver OOMKilled
- Excessive driver GC
- Listener or result collection overload
- Checkpoint initialization fails

The driver is usually the best starting point for application-level root cause.

### Executor failures

Common signals:

- ExecutorLostFailure
- Container OOMKilled
- Shuffle fetch failure
- Task retries exhausted
- Skewed partitions
- Node eviction
- Dynamic allocation churn

Repeated executor loss can eventually fail the driver even when the driver is healthy.

## Memory diagnosis

| Signal | Likely area |
|---|---|
| `java.lang.OutOfMemoryError: Java heap space` | JVM heap sizing or application retention |
| `GC overhead limit exceeded` | Severe heap pressure |
| Pod reason `OOMKilled`, no JVM OOM | Container limit exceeded, possibly overhead/off-heap |
| `Memory Overhead Exceeded` | Increase overhead or reduce native/off-heap use |
| Executors fail on specific partitions | Data skew or unusually large records |

## CPU throttling

Symptoms may include:

- Long batch duration
- Slow task completion
- Heartbeat timeout
- Probe failures
- Increased GC duration

Confirm using container CPU throttling metrics. Do not infer throttling from high CPU usage alone.

## Kafka and streaming

Check:

- Consumer lag
- Partition count and assignment
- Offset progress
- Source rate versus processing rate
- Rebalance frequency
- Checkpoint consistency
- Backpressure

Common failure patterns:

```text
Input grows faster than processing → increasing lag
Checkpoint unavailable → query cannot resume safely
Partition skew → a few tasks dominate batch duration
Frequent executor churn → unstable throughput and retries
```

## Iceberg and catalog

| Signal | Possible cause |
|---|---|
| Commit conflict | Concurrent writers or stale table reference |
| Deleted/missing manifest | Concurrent maintenance, stale metadata, incorrect cleanup |
| Catalog unavailable | Network, auth, DB, or catalog service failure |
| AccessDenied on object storage | Workload identity or bucket policy |
| Schema mismatch | Producer evolution or incompatible writer configuration |

## Spark Operator and cleanup

Check:

- Operator logs
- CR state and events
- Service account permissions
- Child `ownerReferences`
- `successfulRunHistoryLimit`
- `failedRunHistoryLimit`
- TTL or cleanup controller behavior

History limits control retained custom resources. Child cleanup depends on operator behavior and Kubernetes garbage collection.

## Safe production rules

Never:

- Delete production checkpoints
- Delete SparkApplication resources as a diagnostic shortcut
- Restart the Spark Operator without an approved runbook
- Modify executor counts or memory during diagnosis
- Remove Iceberg metadata or manifests manually
