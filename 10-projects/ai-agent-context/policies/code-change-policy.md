# Code Change Policy

## Purpose

Ensure agent-authored changes are narrow, reviewable, maintainable, and easy to validate.

## Before editing

Determine:

- The requested behavior
- The smallest affected area
- Existing project conventions
- Relevant tests
- Compatibility constraints
- Whether the change affects public APIs, schemas, configuration, or deployment behavior

Do not silently broaden the scope.

## Change rules

- Prefer the smallest correct change.
- Preserve unrelated behavior.
- Follow existing naming, formatting, dependency, and test conventions.
- Do not add dependencies unless necessary.
- Avoid speculative refactors during bug fixes.
- Do not modify generated files unless the normal generation process is used.
- Update documentation and configuration when behavior changes.

## Comments

Do not add comments that merely restate the code.

Add or update comments only when they explain:

- Non-obvious intent
- Security assumptions
- Concurrency guarantees
- Protocol or schema constraints
- External system behavior
- Important tradeoffs
- Temporary workarounds and their removal condition

Remove outdated comments.

## Validation

Use the strongest practical checks available:

- Targeted unit tests
- Integration tests
- Static analysis
- Formatting
- Compilation or build
- Configuration rendering
- Terraform validation or plan
- Helm template rendering
- Schema compatibility checks

Do not claim a check passed unless it was run successfully.

## Handoff

Report:

- Files changed
- Behavioral impact
- Tests and checks run
- Checks not run and why
- Risks or follow-up work
- User-owned Git or deployment actions
