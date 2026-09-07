# Fixture Policy

Fixtures provide reproducible operational evidence without exposing production data.

## Allowed fixture types

- Synthetic application and infrastructure logs.
- Sanitized Terraform plan output.
- Synthetic Prometheus alerts and metrics snapshots.
- Sanitized incident timelines.
- Synthetic or sanitized runbooks.

## Sanitization checklist

- Replace names, emails and user identifiers.
- Replace cloud account, subscription and project identifiers.
- Replace IP addresses, hostnames and internal URLs when sensitive.
- Remove access keys, tokens, passwords, cookies and authorization headers.
- Remove customer payloads and business-confidential values.
- Preserve only the structure needed by the test case.

## Fixture metadata

Each fixture should record:

- Fixture ID and version.
- Source type.
- Synthetic or sanitized classification.
- Scenario and expected behavior.
- Creation date.
- Sanitization review status.

Do not commit a fixture until the sanitization checklist has been reviewed manually.
