---
name: code-review
description: Review application code changes for correctness, reliability, maintainability, security, performance, observability, and test coverage.
---

# Code Review

## Use when

- Reviewing a diff, pull request, branch, commit, or local code change
- Assessing implementation quality
- Identifying bugs and missing tests

## Do not use for

- Terraform, Helm, Kubernetes, IAM, or environment configuration review; use `iac-review`.
- Live production diagnosis; use `incident-diagnosis`.

## Review priorities

Review in this order:

1. Correctness
2. Data loss or corruption risk
3. Security and authorization
4. Concurrency and consistency
5. Error handling and recovery
6. Backward compatibility
7. Performance and resource use
8. Observability
9. Tests
10. Maintainability

## Workflow

### 1. Understand intent

Read the ticket, PR description, tests, and surrounding code.

State the intended behavior before judging the implementation.

### 2. Inspect the diff and call paths

Do not review only changed lines. Inspect:

- Callers
- Downstream consumers
- Data models
- Configuration
- Error paths
- Tests

### 3. Check correctness

Look for:

- Incorrect conditions
- Null and empty input behavior
- Off-by-one errors
- Partial failure handling
- Incorrect retries
- Non-idempotent operations
- Resource leaks
- Exception swallowing

### 4. Check distributed-system concerns

When relevant:

- Duplicate delivery
- Ordering assumptions
- Transaction boundaries
- Timeout behavior
- Retry amplification
- Poison messages
- Schema evolution
- Cache consistency
- Race conditions

### 5. Check security

Inspect:

- Authentication and authorization
- Input validation
- Injection risk
- Secret exposure
- Sensitive logging
- Path and URL handling
- Dependency changes

### 6. Check observability

Confirm that important paths expose enough information through:

- Structured logs
- Metrics
- Traces
- Error classification
- Correlation IDs

Avoid asking for excessive logging.

### 7. Check tests

Look for tests covering:

- Normal behavior
- Boundary conditions
- Failure and retry behavior
- Compatibility
- Concurrency, when relevant

### 8. Produce findings

Findings must be actionable and prioritized.

Each finding should include:

- Severity: Critical, High, Medium, Low
- File and line
- Problem
- Impact
- Suggested correction

Do not invent findings to fill categories.

## Output

Start with findings. Then include:

- Review summary
- Positive aspects
- Missing validation
- Overall recommendation

If no significant findings exist, say so explicitly and mention residual risk.
