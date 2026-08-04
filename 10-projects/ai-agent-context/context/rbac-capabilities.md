# RBAC and Capability Model

## Purpose

Document the expected access model and how skills should behave when permissions are limited.

## Default production identity

Use a dedicated read-only identity for production.

Expected capabilities:

```text
get/list/watch workloads
read Pod logs
read Events
read Services and Ingresses
read HPAs and disruption budgets
read non-secret ConfigMaps
inspect metrics when available
```

Expected restrictions:

```text
no create/update/patch/delete
no exec
no port-forward
no secret value access
no deployment or workflow execution
```

## Permission check

When access is uncertain:

```bash
kubectl auth can-i get pods -n <namespace>
kubectl auth can-i get pods/log -n <namespace>
kubectl auth can-i get sparkapplications -n <namespace>
```

Do not repeatedly probe forbidden operations.

## Access-denied behavior

When a command returns `403 Forbidden`:

1. Report the unavailable resource.
2. Use a documented inference path when one exists.
3. Continue with remaining checks.
4. Lower confidence if the missing resource matters.
5. Do not attempt privilege escalation.

## Common inference paths

| Unavailable resource | Possible alternatives |
|---|---|
| Nodes | Pod `spec.nodeName`, scheduling events, metrics dashboards |
| Argo Rollout CR | Pod labels, ReplicaSets, HPA target, deployment dashboard |
| SparkApplication CR | Driver labels, driver logs, executor Pods, operator logs |
| ExternalSecret CR | Pod events, missing Secret references, controller logs |
| Cloud resource details | IaC declaration, deployment outputs, application errors |

## Authentication failures

If credentials are expired, report the exact re-authentication action appropriate to the environment and continue with repository, CI/CD, and ticket evidence.
