---
sidebar_position: 6
---
# Versioning

Payrail uses URL-based versioning to ensure backwards compatibility. All API endpoints are prefixed with a version number.

---

## Current version
```
https://payrail-api.onrender.com/api/v1
```

The current stable version is **v1**.

---

## Version policy

| Version | Status | Notes |
|---------|--------|-------|
| v1 | Active | Current stable version |

Payrail follows these versioning principles:

- **Non-breaking changes** — new fields, new endpoints — are added to the current version without a version bump
- **Breaking changes** — removed fields, changed response structures — always result in a new version
- **Deprecated versions** — receive a minimum 6 months notice before being retired

---

## Breaking vs non-breaking changes

**Non-breaking changes (no version bump):**
- Adding new optional request fields
- Adding new fields to responses
- Adding new endpoints
- Adding new event types to webhooks

**Breaking changes (new version):**
- Removing or renaming fields
- Changing field types
- Changing authentication method
- Removing endpoints

---

## Version sunset policy

When a new version is released:

1. The previous version enters **deprecated** status
2. A deprecation notice is added to all affected endpoints in the docs
3. After 6 months the deprecated version is retired
4. Retired versions return a `410 Gone` response

---

## Changelog

### v1 — March 2026

Initial release. Includes:
- Customer management
- Payment method management
- Transaction processing
- Refund management
- JWT authentication

---

## Related guides

- [API Reference](api-reference.md)
- [Authentication](authentication.md)