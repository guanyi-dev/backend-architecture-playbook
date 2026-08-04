# Data Handling Policy

## Purpose

Prevent accidental disclosure of secrets, customer information, personal data, and internal operational details.

## Never expose

Do not reproduce:

- Passwords
- API keys
- OAuth tokens
- JWTs
- Session cookies
- Private keys
- Complete database connection strings
- Cloud access keys
- Signed URLs
- Customer payloads
- Personally identifiable information
- Full environment dumps

## Redaction rules

Replace sensitive values with clear placeholders:

```text
Authorization: Bearer [REDACTED]
password=[REDACTED]
postgresql://user:[REDACTED]@host/database
customerId=[REDACTED]
```

When showing logs:

- Include only the minimum lines needed to support the finding.
- Prefer error class, timestamp, request ID, trace ID, component, and sanitized message.
- Avoid copying complete stack traces unless required.
- Remove request bodies and headers unless they are known to be non-sensitive.

## Configuration files

ConfigMaps and plain-text configuration can still contain secrets. Treat all configuration as potentially sensitive until inspected.

Do not read Kubernetes Secrets unless the user explicitly requests it and access policy permits it. Production skills should not retrieve secret values.

## External AI boundary

Assume any content sent to an AI tool may leave the local execution environment unless the platform is explicitly approved for confidential data.

When uncertain:

- Summarize instead of pasting.
- Redact identifiers.
- Use synthetic examples.
- Tell the user what information was withheld.
