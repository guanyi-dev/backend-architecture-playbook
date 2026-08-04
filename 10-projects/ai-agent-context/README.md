# Engineering Agent Context

A reusable, vendor-neutral agent-context repository for engineering work with OpenCode, GitHub Copilot CLI, and compatible tools.

It separates:

- **Policies** — safety and authorization boundaries
- **Context** — platform and repository knowledge
- **Skills** — task workflows
- **Commands** — short user-facing entry points
- **References** — detailed lookup material loaded only when needed

## Customize first

Update these files for your organization:

- `context/team-topology.md`
- `context/repo-map.md`
- `context/workload-map.md`
- `context/rbac-capabilities.md`

Keep policies generic where possible. Store environment-specific names and access constraints in `context/`, not inside every skill.

## Suggested usage

```text
/health payments-api in staging
/diagnose order-consumer CrashLoopBackOff in prod
/verify-deploy release 2.4.1 to prod
/review-code current branch against origin/main
/review-iac PR 123 for production risk
```

## Design principle

Skills describe **what task to perform**.
Policies describe **what boundaries apply**.
Context describes **where the task runs**.
References provide **specialist knowledge**.
