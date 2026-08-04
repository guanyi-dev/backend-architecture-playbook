---
name: iac-review
description: Review Terraform, Terragrunt, Helm, Kubernetes, IAM, networking, and deployment configuration changes for operational risk and correctness.
---

# Infrastructure as Code Review

## Use when

Reviewing:

- Terraform or Terragrunt
- Helm templates and values
- Kubernetes manifests
- IAM or RBAC
- Networking and ingress
- Cloud databases, storage, queues, and clusters
- Deployment configuration

## Review priorities

1. Destructive or replacement risk
2. Production targeting
3. Permission expansion
4. Availability and rollout safety
5. State and dependency correctness
6. Security and secret handling
7. Resource sizing and cost
8. Observability and operability
9. Rollback feasibility
10. Maintainability

## Workflow

### 1. Determine environment and blast radius

Identify:

- Target accounts, clusters, regions, and namespaces
- Shared versus application-specific resources
- Production involvement
- Number of affected services

### 2. Inspect plan-level behavior

For Terraform/Terragrunt, look for:

- Destroy/create replacement
- State address changes
- Provider or module upgrades
- `for_each` or key changes
- Immutable field changes
- Dependency cycles
- Imported or moved resources

Do not claim a plan result unless a plan was run.

### 3. Inspect Kubernetes and Helm behavior

Check:

- Selectors and labels
- Resource names
- Requests and limits
- Probes
- Security context
- Service accounts and RBAC
- Pod disruption budgets
- Affinity and topology constraints
- Upgrade strategy
- Hooks and lifecycle ordering
- CRD compatibility

Render Helm templates when practical.

### 4. Inspect IAM and RBAC

Look for:

- Wildcard resources or actions
- Cluster-wide permissions where namespaced permissions are sufficient
- Privilege escalation paths
- Cross-account trust changes
- Missing conditions
- Service account identity mismatch

### 5. Inspect reliability

Check:

- Multi-AZ or replica behavior
- Backups and retention
- Graceful rollout
- Capacity during deployment
- Dependency readiness
- Retry and timeout settings
- Deletion protection

### 6. Inspect rollback

State whether rollback is:

- Configuration-only
- Requires data migration reversal
- Blocked by immutable infrastructure
- Risky after schema or state changes

### 7. Validate

Use relevant checks:

```text
terraform fmt -check
terraform validate
terragrunt hclfmt --check
helm lint
helm template
kubectl apply --dry-run=client
schema validation
policy-as-code checks
```

Do not execute a real apply.

## Output

Prioritized findings first, followed by:

- Blast radius
- Validation performed
- Deployment risk
- Rollback assessment
- Recommended approval decision
