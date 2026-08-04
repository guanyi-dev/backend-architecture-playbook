# Repository Map

## Purpose

Provide a generic map for correlating application behavior, deployment configuration, and platform infrastructure.

## Default repository categories

| Category | Example repository | Contains |
|---|---|---|
| Application | `service-*` | Business logic, APIs, consumers, tests |
| Shared library | `platform-libraries` | Logging, auth, schemas, client libraries |
| Application deployment | `deploy-applications` | Helm values, image versions, environment bindings |
| Infrastructure | `platform-infrastructure` | Terraform/Terragrunt, clusters, networking, databases |
| Helm charts | `platform-helm-charts` | Reusable Kubernetes templates |
| Schemas | `event-schemas` | Protobuf, Avro, JSON Schema, compatibility rules |
| Documentation | `engineering-docs` | Architecture, runbooks, ownership, standards |

## Local repository convention

Preferred root:

```text
~/repos/
```

Agents should not assume every repository is present.

When a repository exists:

```bash
git -C ~/repos/<repo> fetch --prune
git -C ~/repos/<repo> status --short
git -C ~/repos/<repo> log --oneline origin/main -15
```

Use remote refs for investigation:

```bash
git -C ~/repos/<repo> show origin/main:path/to/file
git -C ~/repos/<repo> diff origin/main...origin/<feature-branch>
```

## Correlation workflow

For a deployed service, locate:

1. Application source repository
2. Build artifact and version
3. Deployment declaration
4. Shared Helm chart or manifest template
5. Infrastructure dependencies
6. Schema dependencies
7. Owning team documentation

## Change ownership

Before recommending a fix, identify which repository should own it:

| Symptom | Likely owner |
|---|---|
| Application exception | Application repository |
| Wrong image version | Deployment repository or CD pipeline |
| Missing resource limits | Application values or shared Helm chart |
| Cluster add-on failure | Infrastructure repository |
| Schema incompatibility | Schema repository and producer/consumer code |
| Repeated cross-service issue | Shared library or platform chart |
