---
name: deployment-verification
description: Verify whether a release, pull request, ticket, or image version reached an environment and whether the deployed workload became healthy.
---

# Deployment Verification

## Use when

- “Did version X deploy?”
- “Did ticket ABC-123 reach staging or production?”
- “Why is the live version different from the declared version?”
- “Did the promotion pipeline succeed?”

## Inputs

At least one of:

- Version or image tag
- Ticket key
- Pull request
- Commit SHA
- Application name

Also resolve the target environment.

## Safety

Read-only. Do not trigger, retry, approve, or promote a deployment.

## Workflow

### 1. Resolve artifact identity

Map the request to:

- Source commit
- Build run
- Artifact image and digest
- Deployment configuration version

### 2. Check CI

Verify:

- Build conclusion
- Test conclusion
- Artifact publication
- Image tag and digest

### 3. Check configuration change

Determine whether the deployment repository declares the requested artifact for the target environment.

Use remote refs without pulling or checking out.

### 4. Check CD execution

Inspect:

- Pipeline or GitOps reconciliation result
- Time of apply
- Failed steps
- Environment approvals

### 5. Check live state

Verify:

- Live image tag or digest
- Workload generation or revision
- Available replicas
- Rollout or Job result
- Relevant post-deployment warnings

### 6. Classify result

Use one of:

- Fully deployed and healthy
- Deployed but unhealthy
- Declared but not applied
- Built but not promoted
- Pipeline failed
- Version drift
- Unable to verify

## Output

Include a compact chain:

```text
Commit → Build → Artifact → Deployment declaration → CD run → Live workload
```

For each stage, report status and evidence.
