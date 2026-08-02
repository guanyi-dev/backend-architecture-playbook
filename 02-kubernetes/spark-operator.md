## Spark Operator

Operator is an expert. We do not deploy driver and executor pods directly. Instead we deploy a sparkApplication custom resourec and let spark operator control the entire spark lifecycle --

The spark operator watched for the new spark application, then automatically create driver pod, assign executor pods, create spark UI, track job status then clear up the resources when job completed.

### Spark driver and executor pods

- In our spark-on-k8s platform, the driver pod ran the application's main() method and control the while spark job. It required executors from k8s, scheduled tasks among them, tracked job status, retried when executor failed.
- The executor performed the actual distributed processing, such as reading data from kafka, transformation, writing to iceberg. 
- If executor failed, the driver rescheduled unfinished part on another executor; If driver failed, the app terminated.

#### Spark restart policy
Here is an example of restart policy for spark job:
```yaml
restartPolicy:
  type: OnFailure
  onFailureRetries: 10
  onFailureRetryInterval: 120
  onSubmissionFailureRetries: 20
  onSubmissionFailureRetryInterval: 120
```

#### Driver pod race condition
We always want to delete driver and executor pods when they complete their jobs or we will face race conditions and block the new submissions. A normal config like below:
```yaml
  spark.kubernetes.driver.deleteOnTermination: "true"
  spark.kubernetes.executor.deleteOnTermination: "true "
```

### Migrate on-prem YARN to Spark-on-EKS
YARN control everything in one place while on EKS we have to deal with separate parts: IAM, networking, secrets, cleanup etc.

YARN spark-submit directly talk to Resource Manager while on k8s the spark operator submit spark job on your behalf based on the SparkApplicatoin CR. The driver service account needs create pod permission, that's granted by RBAC.

On EKS, you need to set the limits or spark could eat the whole node. Memory overhead for spark off-heap usage was the most common OOM trap.
- coreLimit: "1" -- This is a spark concept, not k8s
- memoryOverheadFactor: 0.2 -- JVM + Container overhead

When setup access to MSK, S3, Nessie and Iceberg, we want "credentials without secrets on disk", here is what we did:
- s3, IRSA(IAM Roles for service accounts): The pod inherits the IAM role, not access key anywhere
  ```yaml
  annotations:
      eks.amazonaws.com/role-arn: arn:aws:iam:***:role/spark-s3-access
  ```
- kafka, MSK + IAM auth, same IRSA grants for kafka actions.
- Nessie (catalog) + Iceberg: Spark config passes the catalog URI and warehhouse path:
    ```yaml
    sparkConf:
      spark.sql.catalog.nessie.uri:
      spark.sql.catalog.nessie.ref:
      spark.sql.catalog.nessie.waarehouse:
    ```
- Secrets: We use externalSecrets pull from AWS Secret Manager into k8s secret, mounted as env vars:
  ```yaml
  dataFrom:
    -extract:
      key:"{{account}}/applications/{{namespace}}/{{name}}"
  ```

### Real Spark Infrastructure issue
- We use argoCD for UI integration with k8s jobs. But it is not for spark and the problem is that the argoCD health state is not in sync with our spark targed status --
    - When driver and executor pod created, SparkApplication CRs never reached k8s state and argoCD shows "progressing" forever. This could be solved by adding custom health check to map spark internal status such as:
    - COMPLETED/FAILED -> Healthy, RUNNING -> Progressing.
- Another issue was the driver pod garbage collection. We have scheduled spark jobs. After completion we clean old run via "sucessfulRunHistoryLimit: 1" but we saw hundreds of them.
    - could be a few causes: Spark Operator lack RBAC to delete pods, cleanup controller does not work. In our case, we miss the OwnerReference on child resources, below is my troubleshooting steps:
      - I confirmed that only one successful SparkApplication remained, so the history limit was working correctly.
      - I noticed many completed driver Pods no longer had an active parent SparkApplication. The solution is to add OwnerReference:
      ```yaml
        ownerReferences:
      - apiVersion: sparkoperator.k8s.io/v1beta2
        kind: SparkApplication
        name: my-job
        uid: 3a8d0d9b-1234-5678-abcd-111122223333
        ```
