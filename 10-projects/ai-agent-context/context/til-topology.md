# Platform Topology

## Purpose

Describe the default environment model used by the skills. Replace placeholders with team-specific values while preserving the structure.

## Environment classes

| Environment | Typical purpose | Default agent behavior |
|---|---|---|
| `local` | Developer machine and local containers | Edits and tests allowed |
| `dev` | Shared development integration | Read first; limited changes only when explicitly requested |
| `staging` | Pre-production validation | Read-first; changes require explicit user direction |
| `prod` | Customer-facing production | Strictly read-only |

## Example cluster mapping

| Environment | Cluster context | Common namespaces | Cloud account/profile |
|---|---|---|---|
| dev | `platform-dev` | `apps-dev`, `data-dev`, `observability` | `company-dev` |
| staging | `platform-staging` | `apps-staging`, `data-staging` | `company-staging` |
| prod | `platform-prod-readonly` | `apps-prod`, `data-prod`, `platform-system` | `company-prod-readonly` |

## Scope resolution

Resolve scope from the user’s request, repository path, deployment metadata, ticket, or workload name.

Use these signals in order:

1. Explicit environment and namespace
2. Deployment or incident link
3. Repository environment directory
4. Workload naming conventions
5. Known service ownership map

If production cannot be ruled out, apply the production read-only policy.

## Typical architecture layers

```text
Clients / producers
        ↓
Ingress, APIs, queues, or event brokers
        ↓
Application and transformation services
        ↓
Streaming or batch processing
        ↓
Operational databases and object storage
        ↓
Catalogs, analytics tables, and downstream consumers
```

## Desired-state hierarchy

Use the following model when investigating drift:

```text
Ticket or design intent
        ↓
Application and infrastructure code
        ↓
CI build artifacts
        ↓
Deployment configuration
        ↓
CD execution
        ↓
Live platform state
        ↓
Observed application behavior
```

A mismatch between adjacent layers is often more informative than an isolated workload error.
