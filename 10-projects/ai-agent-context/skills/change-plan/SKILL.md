---
name: change-plan
description: Produce an implementation or remediation plan with affected components, sequencing, validation, rollout, rollback, and production risk. Does not execute production changes.
---

# Engineering Change Plan

## Use when

- The user wants a fix design before implementation.
- A production incident needs a remediation runbook.
- A change spans multiple repositories or systems.
- The user asks how to safely roll out an engineering solution.

## Inputs

Resolve:

- Problem or desired outcome
- Affected services and environments
- Functional requirements
- Reliability and security constraints
- Compatibility requirements
- Change window or rollout constraints

## Workflow

### 1. Define current and target state

Describe:

- Current behavior
- Target behavior
- What is explicitly out of scope

### 2. Identify change ownership

List affected:

- Application repositories
- Deployment configuration
- Infrastructure
- Schemas
- Documentation and runbooks
- Dashboards and alerts

### 3. Evaluate options

For material design choices, compare:

- Benefits
- Risks
- Complexity
- Operational burden
- Reversibility
- Migration requirements

Recommend one option and explain why.

### 4. Sequence implementation

Prefer small reversible steps:

1. Add compatibility or observability first.
2. Deploy non-disruptive prerequisites.
3. Introduce new behavior behind a flag when practical.
4. Validate in lower environments.
5. Roll out gradually.
6. Remove temporary compatibility only after adoption.

### 5. Define validation

Include:

- Unit and integration tests
- Configuration validation
- Load or failure testing
- Metrics and logs to watch
- Success criteria
- Data integrity checks

### 6. Define rollout

Specify:

- Environment order
- Canary or phased rollout
- Required approvals
- Monitoring window
- Stop conditions

### 7. Define rollback

Include:

- Exact rollback trigger
- Reversible configuration or version
- Data compatibility concerns
- Checkpoint or schema constraints
- Post-rollback verification

### 8. Define long-term prevention

Consider:

- Alerts
- SLOs
- Automated validation
- Policy checks
- Runbook updates
- Ownership improvements

## Output

Produce a practical plan, not a generic checklist.

Include:

- Recommended approach
- Affected files/resources
- Ordered implementation steps
- Validation
- Rollout
- Rollback
- Risks and mitigations
- Open questions
