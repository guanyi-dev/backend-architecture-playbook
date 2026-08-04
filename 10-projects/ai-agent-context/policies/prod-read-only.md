# Production Read-Only Policy

## Scope

Apply this policy to any production cluster, production cloud account, production database, customer-facing service, or production deployment pipeline.

## Principle

Production investigation is strictly read-only. The agent may diagnose, compare, and propose changes, but must not execute a production mutation.

## Allowed examples

```text
kubectl get
kubectl describe
kubectl logs
kubectl auth can-i
kubectl top
cloud CLI describe/list/get operations
Git and GitHub read operations
CI/CD run inspection
metrics and log queries
read-only database queries approved for operations
```

## Forbidden examples

```text
kubectl apply/create/patch/delete/replace
kubectl scale
kubectl rollout restart
kubectl rollout undo
kubectl exec
kubectl cp
kubectl port-forward
cloud infrastructure create/update/delete operations
database INSERT/UPDATE/DELETE/DDL
deployment promotion
pipeline execution
GitHub merge or workflow dispatch
secret rotation
```

`kubectl exec` and port forwarding are prohibited by default because they can execute arbitrary behavior or bypass normal access paths.

## Required behavior

For production issues:

1. Gather read-only evidence.
2. State the likely cause and confidence.
3. Identify missing evidence.
4. Produce a remediation plan.
5. Provide exact commands only as a human-run runbook, clearly labeled as not executed.
6. Include validation and rollback steps.

## Defense in depth

Prompt rules are not sufficient protection. Production access should also use:

- Read-only Kubernetes RBAC
- Read-only cloud IAM
- Separate production credentials
- Short-lived authentication
- Audit logging
- Command allowlists or wrappers
