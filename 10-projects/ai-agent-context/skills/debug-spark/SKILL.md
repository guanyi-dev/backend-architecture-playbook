---
name: debug-spark
description: Specialist workflow for Spark-on-Kubernetes failures involving the Spark Operator, driver and executor Pods, scheduling, memory, checkpoints, Kafka, Iceberg, object storage, and catalogs.
---

# Spark Debugging

## Use when

Evidence indicates a Spark-specific problem, including:

- Failed SparkApplication
- Driver or executor Pod failure
- Scheduled Spark runs missing or accumulating
- OOM or CPU throttling
- Checkpoint or offset problems
- Iceberg or catalog commit failures
- Spark Operator reconciliation issues

Use this as a specialist extension to `incident-diagnosis`.

## Spark resource model

```text
SparkApplication or ScheduledSparkApplication
        ↓
Spark Operator reconciliation
        ↓
Driver Pod and Service
        ↓
Executor Pods
        ↓
Kafka / object storage / catalog / database dependencies
```

## Workflow

### 1. Identify application type

Determine:

- Batch or streaming
- Scheduled or manually submitted
- Spark version
- Operator version
- Driver and executor image
- Environment and namespace

### 2. Inspect Spark custom resource when allowed

Check:

- Application state
- Submission attempts
- Driver specification
- Executor specification
- Restart policy
- History limits
- Dynamic allocation
- Dependencies and volumes

If CR access is forbidden, infer from driver labels, Pods, Services, operator logs, and deployment configuration.

### 3. Inspect driver

The driver usually provides the primary application failure signal.

Check:

- Pod phase and termination reason
- Previous logs
- GC pressure
- Memory overhead
- CPU throttling
- API and dependency errors
- SparkContext initialization
- Streaming query termination

### 4. Inspect executors

Look for:

- Repeated executor loss
- OOMKilled
- Eviction
- Failed scheduling
- Shuffle fetch failures
- Task skew
- Lost nodes
- Dynamic allocation churn

### 5. Classify resource failure

Distinguish:

- Java heap OOM
- Container OOMKilled
- Memory overhead exhaustion
- CPU throttling
- Ephemeral storage exhaustion
- Node pressure eviction
- Unschedulable resource request

### 6. Inspect streaming state

For streaming workloads, check:

- Checkpoint path
- Permissions
- Corruption or incompatible metadata
- Source offsets
- Consumer lag
- Query progress
- Restart semantics
- Exactly-once assumptions

Never delete or alter a production checkpoint.

### 7. Inspect Iceberg and catalog signals

Look for:

- Commit conflicts
- Deleted or missing manifests
- Catalog connectivity
- Authentication failures
- Object storage permission errors
- Schema or partition evolution issues
- Concurrent writer behavior

### 8. Inspect operator and ownership behavior

For orphaned resources or failed cleanup:

- Confirm parent custom resource retention
- Inspect `ownerReferences`
- Check operator RBAC
- Check operator reconciliation errors
- Check history limits and TTL behavior

Remember:

```text
History limits control retained Spark custom resources.
Kubernetes garbage collection of child resources depends on valid owner references.
```

### 9. Build a precise diagnosis

State whether the failure is primarily:

- Application code
- Spark configuration
- Resource sizing
- Kubernetes scheduling
- Operator/controller
- Authentication/authorization
- Kafka/source
- Storage/catalog
- Data-specific

## Output

Use the Spark section of `references/report-templates.md` and cite the exact driver or executor signal supporting the conclusion.
