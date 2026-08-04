# Global Agent Rules

This repository defines shared behavior for engineering agents used through tools such as OpenCode, GitHub Copilot CLI, and compatible agent runners.

The files are organized into four layers:

1. **Policies** define non-negotiable safety and change boundaries.
2. **Context** describes the environment, repositories, workloads, and access model.
3. **Skills** define task-specific workflows.
4. **Commands** are thin entry points that route user requests to the correct skill.

## Core Operating Principles

### 1. Evidence before action

Do not guess about system state when it can be inspected. Separate:

- **Observed fact** — directly supported by code, configuration, logs, events, metrics, or command output.
- **Inference** — a conclusion supported by one or more observed facts.
- **Unknown** — information that is unavailable, inaccessible, or not yet checked.

Do not present an inference as a confirmed fact.

### 2. Least privilege

Use the smallest set of tools and permissions required for the task. Production access is read-only unless a separately approved workflow explicitly states otherwise.

### 3. Preserve the user’s workspace

Do not alter Git history, switch branches, discard local work, or change local repository state unless the user explicitly requests it and the active policy permits it.

Prefer inspecting remote refs directly after `git fetch --prune`.

### 4. Progressive investigation

Use the lightest workflow that can answer the request:

- Lookup
- Health check
- Deployment verification
- Incident diagnosis
- Specialist debugging
- Change planning

Do not perform a full incident investigation for a simple version lookup.

### 5. Human-controlled changes

Agents may prepare code changes when allowed, but the user owns staging, committing, pushing, merging, deploying, and production changes.

### 6. Minimal sensitive output

Never reproduce credentials, authentication tokens, private keys, session cookies, customer data, personal data, or complete connection strings. Redact sensitive values before presenting logs or configuration.

### 7. Clear completion criteria

Every task should end with:

- What was examined
- What was found
- What remains unknown
- What was changed, if anything
- What the user must do next

## Required Policies

Apply these policies to all tasks:

- `policies/git-safety.md`
- `policies/data-handling.md`
- `policies/code-change-policy.md`

Apply `policies/prod-read-only.md` whenever a production environment, production account, production cluster, or customer-facing workload is involved.

## Skill Selection

Use the following mapping:

| User intent | Skill |
|---|---|
| Is this service healthy? What is deployed? | `health-check` |
| Why is this failing or degraded? | `incident-diagnosis` |
| Did a release or ticket reach an environment? | `deployment-verification` |
| Review an application code change | `code-review` |
| Review Terraform, Helm, Kubernetes, IAM, or other IaC | `iac-review` |
| Diagnose a Spark-specific issue | `debug-spark` |
| Propose a safe implementation or remediation plan | `change-plan` |

If several skills apply, start with the broadest task skill and use specialist skills only when the evidence requires them.
